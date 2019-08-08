---
layout: default
title: Building a PGP SKS Keyserver
displaytitle: Building a PGP SKS Keyserver
permalink: /guides/building-server/
---

<div class="alert alert-warning">
  <strong>Notice!</strong> This page is written with Ubuntu 18.04 LTS in mind, please see the <a href='/guides/building-server/ubuntu-16/'>Ubuntu 16.04 LTS</a> or <a href='/guides/building-server/ubuntu-14/'>Ubuntu 14.04 LTS</a> versions of this document.
</div>

A Key Server is used to distribute [PGP/GPG](http://en.wikipedia.org/wiki/Pretty_Good_Privacy) keys between different users.  One of the most popular key servers for use with pgp/gpg is the [sks keyserver](https://bitbucket.org/skskeyserver/sks-keyserver). This document will walk you through downloading, installing, and setting up a sks keyserver on [Ubuntu](http://www.ubuntu.com/) 18.04 LTS.

## Building your own PGP SKS Server
Building a [SKS](https://bitbucket.org/skskeyserver/sks-keyserver) server is a pretty straight forward project if you are use to running servers.

To build a production SKS Server, you must...

* [Building the SKS Daemon](#building-the-sks-daemon)
* [Download the needed database files](#download-the-needed-database-files)
* [Import the downloaded databases files](#import-the-downloaded-databases-files)
* [Configure your web-server](#configure-your-web-server)
* [Start the SKS Daemon](#start-the-sks-daemon)
* [Configure your web-server](#configure-your-web-server)
* [Install the SKS webpage on your server](#install-the-sks-webpage-on-your-server)
* [Start the SKS Daemon](#start-the-sks-daemon)

## Building the SKS Daemon
The following is for [Ubuntu](http://www.ubuntu.com/) 18.04 LTS

    apt-get -y install gcc ocaml libdb-dev gnupg nginx wget

After installing the required software, you need to download SKS

    gpg --keyserver hkp://pool.sks-keyservers.net --trust-model always --recv-key 0x41259773973a612a
    wget https://bitbucket.org/skskeyserver/sks-keyserver/downloads/sks-1.1.6.tgz
    wget  https://bitbucket.org/skskeyserver/sks-keyserver/downloads/sks-1.1.6.tgz.asc
    gpg --keyid-format long --verify sks-1.1.6.tgz.asc

The output of the last command should be

<pre>
gpg: Signature made Mon 05 May 2014 02:06:51 PM CDT
gpg:                using RSA key 41259773973A612A
gpg: Good signature from "SKS Keyserver Signing Key"
</pre>

Now, untar the software

    tar -xzf sks-1.1.6.tgz
    cd sks-1.1.6

Next copy the **Makefile.local.unused** to **Makefile.local** and change `ldb-4.6` to `ldb-5.3` for Ubuntu.

    cp Makefile.local.unused Makefile.local
    sed -i 's/ldb\-4.6/ldb\-5.3/' Makefile.local

Last, build the software

    make dep
    make all
    make install

**Note:** There is a bug in the `sks_build.sh` file where it has the wrong location of the sks program.  To update,
`sks_build.sh`, run the below command.

    sed -i 's/sbin/local\/bin/g' /usr/local/bin/sks_build.sh

## Configure the sks-keyserver

**/var/lib/sks/sksconf**

<pre>
# /var/lib/sks/sksconf

debuglevel: 3

# Set the hostname of your server
hostname:                       --keyserver-hostname--

hkp_address:                    127.0.0.1
hkp_port:                       11371
recon_port:                     11370

# Set the PGP ID for the Server Contact
server_contact:                 --contact-pgp-id--

initial_stat:
disable_mailsync:
membership_reload_interval:     1
stat_hour:                      12

max_matches:                    500
</pre>

## Download the needed database files
Rather than starting with an empty database and attempting to populate it by syncing with
other keyservers (a bad idea because it loads up your peers with lots of traffic and will probably
fail anyway with deadlocks in the conflict resolution system) we&#39;ll grab a static dump from an
existing SKS server. Currently the only known source is:

* <a href="http://keyserver.mattrude.com/dump/">http://keyserver.mattrude.com/dump/</a> - Generated every DAY
* <a href="ftp://ftp.prato.linux.it/pub/keyring/">ftp://ftp.prato.linux.it/pub/keyring/</a> - Generated every Wednesday
* <a href="http://keyserver.borgnet.us/dump">http://keyserver.borgnet.us/dump</a> - Generated every Sunday

### Download Keydump
The keydump is about 7.3GB as of Oct 2015, increasing at a rate of about one gigabyte per year, so fetching it will take a long time. It&#39;s
divided into a bunch of individual numbered files so you&#39;ll need to fetch all of them. Because
I&#39;m too lazy to spend 8 hours sitting there doing it manually I did it like this:

    mkdir -p /var/lib/sks/dump
    cd /var/lib/sks/dump
    wget -crp -e robots=off --level=1 --cut-dirs=3 -nH \
    -A pgp,txt https://keyserver.mattrude.com/dump/current/

Many hours later, check that all the pieces downloaded correctly by comparing their checksums
against the list published by the dump provider:

    md5sum -c metadata-sks-dump.txt


## Import the downloaded databases files

There are two ways to do this: either a full build (which reads in the dump you just downloaded
and leaves you with a complete, self-contained database) or a fastbuild (which just references
the dump and requires it to be left in place after the fastbuild is complete). I started doing
a full build, it looked like it was going to take forever so I aborted it and switched to a
quickbuild. On the 4-processor machine I was using it still took in the order of 40 minutes to
run so this might take a while.

You need to be in the basedir when running this and the dumps have to be in a sub-directory
called `dump` (which they should be if you followed the steps above), so:

    cd /var/lib/sks

Then run the build

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

If you edit the `sks_build.sh` script you'll discover it's just a shell script which calls SKS
itself to do the heavy lifting. If you have trouble with lack of memory you may need to tweak the
script a bit: in particular the `-n 10` flag used in the fastbuild call is a multiple of 15,000
keys to load at a time. The default setting therefore loads 150,000 keys at a time which could
cause your machine to go into swap, and changing to something like `-n 2` will cause it to load
only 30,000 at a time instead and possibly complete the job faster. The trick is to load as
many as possible in each pass without hitting swap - if that happens, performance falls through
the floor and you may as well abort it and start again (after deleting the KDB and PTree
directories created by the aborted import).

If all goes smoothly you&#39;ll end up with `KDB` and `PTree` directories in `/var/lib/sks`.

### DB_CONFIG file

Before starting the sks daemon, the `KDB` directory should have the `DB_CONFIG` file from the 
source repository.  You may copy the file from the source directory or, you may copy the below
file and place it in `/var/lib/sks/KDB`.

**/var/lib/sks/KDB/DB_CONFIG**
```
#************************************************************************#
#* DB_CONFIG - Sample Berkeley DB tunables for use with SKS             *#
#*                                                                      *#
#* Copyright (C) 2011, 2012, 2013  John Clizbe                          *#
#*                                                                      *#
#* This file is part of SKS.  SKS is free software; you can             *#
#* redistribute it and/or modify it under the terms of the GNU General  *#
#* Public License as published by the Free Software Foundation; either  *#
#* version 2 of the License, or (at your option) any later version.     *#
#*                                                                      *#
#* This program is distributed in the hope that it will be useful, but  *#
#* WITHOUT ANY WARRANTY; without even the implied warranty of           *#
#* MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU    *#
#* General Public License for more details.                             *#
#*                                                                      *#
#* You should have received a copy of the GNU General Public License    *#
#* along with this program; if not, write to the Free Software          *#
#* Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA 02111-1307  *#
#* USA or see <http://www.gnu.org/licenses/>.                           *#
#************************************************************************#

set_mp_mmapsize         268435456
set_cachesize    0      134217728 1
set_flags               DB_LOG_AUTOREMOVE
set_lg_regionmax        1048576
set_lg_max              104857600
set_lg_bsize            2097152
set_lk_detect           DB_LOCK_DEFAULT
set_tmp_dir             /tmp
set_lock_timeout        1000
set_txn_timeout         1000
mutex_set_max           65536
```

## Configure your web-server

Inorder to be part of the [sks pool](https://sks-keyservers.net/status/), among other things, you need to route your sks traffic threw a web proxy.  If you followed these instructions you should have already installed the nginx software on your server, all you should have to do is copy the below config into the `/etc/nginx/nginx.conf` file on your server and change the IP addresses to meeting your setup.

You will need to change `--IPv4-Address--` to be your IPv4 IP address, and if you have one, change `--IPv6-Address--` to be your IPv6 address.  If you do not have a IPv6 address on your server, you should remove that line from the configuraton.

**/etc/nginx/nginx.conf**
{% highlight config %}
#/etc/nginx/nginx.conf

user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
    worker_connections 768;
}

http {
    sendfile    on;
    tcp_nopush  on;
    tcp_nodelay on;
    client_max_body_size 8m;

    access_log  /var/log/nginx/access.log;
    error_log   /var/log/nginx/error.log;
    rewrite_log on;

    include /etc/nginx/mime.types;

    #----------------------------------------------------------------------
    # OpenPGP Public SKS Key Server
    #----------------------------------------------------------------------

    server {
        listen 80 default_server;
        listen [::]:80 default_server;
        listen --IPv4-Address--:11371 default_server;
        listen [--IPv6-Address--]:11371 default_server;

        server_name *.sks-keyservers.net;
        server_name *.pool.sks-keyservers.net;
        server_name pgp.mit.edu;
        server_name keys.gnupg.net;

        root /var/www/html;

        rewrite ^/stats /pks/lookup?op=stats;
        rewrite ^/s/(.*) /pks/lookup?search=$1;
        rewrite ^/search/(.*) /pks/lookup?search=$1;
        rewrite ^/g/(.*) /pks/lookup?op=get&search=$1;
        rewrite ^/get/(.*) /pks/lookup?op=get&search=$1;
        rewrite ^/d/(.*) /pks/lookup?op=get&options=mr&search=$1;
        rewrite ^/download/(.*) /pks/lookup?op=get&options=mr&search=$1;

        location /pks {
            proxy_pass         http://127.0.0.1:11371;
            proxy_pass_header  Server;
            add_header         Via "1.1 --keyserver-hostname--:11371 (nginx)";
            proxy_ignore_client_abort on;
            client_max_body_size 8m;
        }
    }
}
{% endhighlight %}

Once you have copied this file into `/etc/nginx/nginx.conf`, you need to restart nginx by running the following command.

    service nginx restart

## Install the SKS webpage on your server

Now we need to install a webpage so visitors are able to interact with your new keyserver via their web browser.  The sks-keyserver project has 3 older sites that come with the default install, these sites may be found [in the source](https://bitbucket.org/skskeyserver/sks-keyserver/src/40280f59d0f503da1326972757168aa42335573f/sampleWeb/?at=default) on bitbucket.

There are obviously meny options besides the three provided by sks-keyserver.  Were going to install [pgpkeyserver-lite](https://github.com/mattrude/pgpkeyserver-lite), a example of this site may be found on [keys.therudes.com](http://keys.therudes.com/).  The install process is pretty straightforward, we will download the tarball, extract it, drop into the html directory we setup above (`/var/www/html`), and lastly update the infromation on the site to reflect your setup.

**Download & extract the tarball**

    curl -Ls https://github.com/mattrude/pgpkeyserver-lite/tarball/master -o pgpkeyserver-lite.tgz && \
    mkdir /var/www/html && tar -xzf pgpkeyserver-lite.tgz --directory /var/www/html --strip 1

**Modify the site**

After downloading and extracting the tarball, you need to modify the site to reflect the setup of your keyserver.  There are two sections that need to be replaced. first you need to replace all instances of `###ENTERNAMEHERE###` with your own name. Next, replace all instances of `###ENTERPUBLICKEYHERE###` with your public key. Or you may of course modify the site in anyway you wish.

## Start the SKS Daemon

There are two may ways of starting/stopping the sks daemons, via an init script and via a systemd script.

You should only run ONE of the below systems, systemd or INIT, you should **NOT** run both at the same time!

### Run via systemd Script

The systemd scripts will automatically restart the sks daemons if they crash, and start them and close the correctly when the system boots or shutsdown.

The full how-to may be found on the [Adding systemd services on a SKS-Keyserver](/guides/systemd-services/) page.

#### Systemd Services Install

First, download the needed service files and add them to your `/etc/systemd/system/` directory.  Note, these config assume the sks daemon lives at `/usr/local/bin/sks` if yours is in a diffrent spot, you will need to update both files to point to your install locaction.

**/etc/systemd/system/sks-db.service**
```bash
[Unit]
Description=SKS-Keyserver DB Instance
After=network.target

[Service]
Type=simple
User=debian-sks
Group=debian-sks
WorkingDirectory=/var/lib/sks
ExecStart=/usr/local/bin/sks db
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

**/etc/systemd/system/sks-recon.service**
```bash
[Unit]
Description=SKS-Keyserver Recon Instance
Before=network.target
Wants=network.target

[Service]
Type=simple
User=debian-sks
Group=debian-sks
WorkingDirectory=/var/lib/sks
ExecStart=/usr/local/bin/sks recon
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

#### Systemd Services Setup

After you have the services installed, you must enable the services.

```bash
systemctl daemon-reload
systemctl enable sks-db.service
systemctl enable sks-recon.service
```

### Run via INIT Script

**Do NOT add this init script if you have already added the systemd scripts, they can not live on the same system at the same time.**

You will can install this init script like you do any other.  Copy the below file into `/etc/init.d/sks` and make sure the file is executable,
then run `service sks start` to start the service.

**/etc/init.d/sks**
{% highlight bash %}
#! /bin/sh

DAEMON=/usr/local/bin/sks
DIR=/var/lib/sks

test -e $DAEMON || exit 0
test -d $DIR || exit 0

case "$1" in
        start)
                cd $DIR
                echo -n "Starting SKS:"
                echo -n \ sks_db
                $DAEMON db &
                echo -n \ sks_recon
                $DAEMON recon &
                echo "."
        ;;
        stop)
                echo -n "Stopping SKS:"
                killall sks
                while [ "`pidof sks`" ]; do sleep 1; done # wait until SKS processes have exited
                echo "."
        ;;
        restart)
                $0 stop
                sleep 1
                $0 start
        ;;
        *)
                echo "Usage: $0 {start|stop|restart}"
                exit 1
        ;;
esac

exit 0
{% endhighlight %}
