---
layout: default
title: Building a SKS Server
permalink: /doc/building-server/
---

## Pre-Populate Database
Rather than starting with an empty database and attempting to populate it by syncing with 
other keyservers (a bad idea because it loads up your peers with lots of traffic and will probably 
fail anyway with deadlocks in the conflict resolution system) we'll grab a static dump from an
existing SKS server. Currently the only known source is:


* <a href="http://keyserver.mattrude.com/dump/">http://keyserver.mattrude.com/dump/</a> - Generated every Friday
* <a href="ftp://ftp.prato.linux.it/pub/keyring/">ftp://ftp.prato.linux.it/pub/keyring/</a> - Generated every Wednesday
* <a href="http://keyserver.borgnet.us/dump">http://keyserver.borgnet.us/dump</a> - Generated every Sunday

### Download Keydump
The keydump is about 4.3GB as of May 2011, so fetching it will take a long time. It's 
divided into a bunch of individual numbered files so you'll need to fetch all of them. Because
I'm too lazy to spend 8 hours sitting there doing it manually I did it like this:

    mkdir /var/lib/sks/dump
    cd /var/lib/sks/dump
    wget --recursive --timestamping --level=1 --cut-dirs=3 \
    --no-host-directories http://keyserver.mattrude.com/dump/current/

Many hours later, check that all the pieces downloaded correctly by comparing their checksums 
against the list published by the dump provider:

    md5sum -c metadata-sks-dump.txt

### Build Local Database
There are two ways to do this: either a full build (which reads in the dump you just downloaded 
and leaves you with a complete, self-contained database) or a fastbuild (which just references
the dump and requires it to be left in place after the fastbuild is complete). I started doing 
a full build, it looked like it was going to take forever so I aborted it and switched to a
quickbuild. On the 4-processor machine I was using it still took in the order of 40 minutes to 
run so this might take a while.

You need to be in the basedir when running this and the dumps have to be in a sub-directory 
called "dump" (which they should be if you followed the steps above), so:

    cd /var/lib/sks
    /usr/local/bin/sks_build.sh

On the next screen, choose **2**.

    Please select the mode in which you want to import the keydump:

    1 - fastbuild
        only an index of the keydump is created and the keydump cannot be
        removed.
    
    2 - normalbuild
    
        all the keydump will be imported in a new database. It takes longer
        time and more disk space, but the server will run faster (depending
        from the source/age of the keydump).
        The keydump can be removed after the import.
    
    Enter enter the mode (1/2): 2

If you edit the sks_build.sh script you'll discover it's just a shell script which calls SKS 
itself to do the heavy lifting. If you have trouble with lack of memory you may need to tweak the 
script a bit: in particular the "-n 10" flag used in the fastbuild call is a multiple of 15,000 
keys to load at a time. The default setting therefore loads 150,000 keys at a time which could 
cause your machine to go into swap, and changing to something like "-n 2" will cause it to load 
only 30,000 at a time instead and possibly complete the job faster. The trick is to load as 
many as possible in each pass without hitting swap - if that happens, performance falls through 
the floor and you may as well abort it and start again (after deleting the KDB and PTree 
directories created by the aborted import).

If all goes smoothly you'll end up with *KDB* and *PTree* directories in **/var/lib/sks**.
