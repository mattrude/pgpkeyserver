---
layout: default
title: Building a PGP Key Server
permalink: /guides/building-server/
---

A Key Server is used to distribute [PGP/GPG](http://en.wikipedia.org/wiki/Pretty_Good_Privacy) keys between different users.  One of the most popular key servers for use with pgp/gpg is the [sks key-server](https://bitbucket.org/skskeyserver/sks-keyserver). This document will walk you through downloading, installing, and setting up a sks key-server on [Ubuntu](http://www.ubuntu.com/) 14.04 LTS.

## Building your own PGP SKS Server
Building a [SKS](https://bitbucket.org/skskeyserver/sks-keyserver) server is a pretty straight forward project if you are use to running servers.

To build a production SKS Server, you must...

* [Building the SKS Daemon](#building-the-sks-daemon)
* [Download the needed database files](#download-the-needed-database-files)
* [Import the downloaded databases files](#import-the-downloaded-databases-files)
* [Configure your web-server](#configure-your-web-server)
* [Start the SKS Daemon](#start-the-sks-daemon)

## Building the SKS Daemon
The following is for [Ubuntu](http://www.ubuntu.com/) 14.04 LTS

{% highlight bash %}
apt-get -y install gcc ocaml libdb6.0-dev ssmtp
{% endhighlight %}

After installing the required software, you need to download SKS

{% highlight bash %}
gpg --keyserver hkp://pool.sks-keyservers.net --recv-key 0x0B7F8B60E3EDFAE3
wget https://bitbucket.org/skskeyserver/sks-keyserver/downloads/sks-1.1.5.tgz
wget  https://bitbucket.org/skskeyserver/sks-keyserver/downloads/sks-1.1.5.tgz.asc
gpg --keyid-format long --verify sks-1.1.5.tgz.asc
{% endhighlight %}

Now, untar the software

{% highlight bash %}
tar -xzf sks-1.1.5.tgz
cd sks-1.1.5
{% endhighlight %}

Next copy the **Makefile.local.unused** to **Makefile.local** and change "ldb-4.6" to "ldb-6.0" for Ubuntu.

{% highlight bash %}
cp Makefile.local.unused Makefile.local
sed -i 's/ldb\-4.6/ldb\-6.0/' Makefile.local
{% endhighlight %}

Last, build the software

{% highlight bash %}
make dep
make all
make install
{% endhighlight %}

## Download the needed database files
Rather than starting with an empty database and attempting to populate it by syncing with
other keyservers (a bad idea because it loads up your peers with lots of traffic and will probably
fail anyway with deadlocks in the conflict resolution system) we&#39;ll grab a static dump from an
existing SKS server. Currently the only known source is:

* <a href="http://keyserver.mattrude.com/dump/">http://keyserver.mattrude.com/dump/</a> - Generated every DAY
* <a href="ftp://ftp.prato.linux.it/pub/keyring/">ftp://ftp.prato.linux.it/pub/keyring/</a> - Generated every Wednesday
* <a href="http://keyserver.borgnet.us/dump">http://keyserver.borgnet.us/dump</a> - Generated every Sunday

### Download Keydump
The keydump is about 6.3GB as of Oct 2014, so fetching it will take a long time. It&#39;s
divided into a bunch of individual numbered files so you&#39;ll need to fetch all of them. Because
I&#39;m too lazy to spend 8 hours sitting there doing it manually I did it like this:

{% highlight bash %}
mkdir /var/lib/sks/dump
cd /var/lib/sks/dump
wget -c -r -p -e robots=off --timestamping --level=1 --cut-dirs=3 \
--no-host-directories http://keyserver.mattrude.com/dump/current/
{% endhighlight %}

Many hours later, check that all the pieces downloaded correctly by comparing their checksums
against the list published by the dump provider:

{% highlight bash %}
md5sum -c metadata-sks-dump.txt
{% endhighlight %}

## Import the downloaded databases files
There are two ways to do this: either a full build (which reads in the dump you just downloaded
and leaves you with a complete, self-contained database) or a fastbuild (which just references
the dump and requires it to be left in place after the fastbuild is complete). I started doing
a full build, it looked like it was going to take forever so I aborted it and switched to a
quickbuild. On the 4-processor machine I was using it still took in the order of 40 minutes to
run so this might take a while.

You need to be in the basedir when running this and the dumps have to be in a sub-directory
called "dump" (which they should be if you followed the steps above), so:

{% highlight bash %}
cd /var/lib/sks
{% endhighlight %}

{% highlight bash %}
/usr/local/bin/sks_build.sh
{% endhighlight %}

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

If all goes smoothly you&#39;ll end up with *KDB* and *PTree* directories in **/var/lib/sks**.

## Configure your web-server

## Start the SKS Daemon

## Patches for the sks-keyserver software

### SKS-Keyserver v1.1.5

* [sks-1.1.5-download-txt.patch](https://gist.github.com/mattrude/709b2726ceb9d2d3386b) - Patch to change the downloadable "pgpkey.asc" file to a txt file, viewable in a web browser. ([example](http://keyserver.mattrude.com/pks/lookup?search=0x27143affdd23bf73&options=mr&op=get)) - [download](https://gist.githubusercontent.com/mattrude/709b2726ceb9d2d3386b/raw/sks-1.1.5-download-txt.patch)
