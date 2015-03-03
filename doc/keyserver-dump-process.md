---
layout: default
title: Keyserver Dump Process
permalink: /guides/dump-process/
description: A simple script to export a sks keyserver dump database
tags: sks, sks-keyserver, sks server dump, PGP, GnuPG
---

# SKS Keyserver Dump Process

This script is intended to be ran from cron, since sks requires the db process to be stopped before a dump can be started.  

    0 0 * * * /usr/local/bin/sks-dump-script.sh &

## Key Server Dump Script

{% highlight bash %}
#!/bin/bash

# This script will stop the sks server, dump its contents to
# the $PREDIR, then restart the sks server.

COUNT="10000"
SKSDATE=`date +%Y-%m-%d`
USER="debian-sks"
INDIR="/var/lib/sks"
PREDIR="/external/sks-dump"
OUTDIR="$PREDIR/$SKSDATE"
TZ='UTC'

for DEL in `ls -1t $PREDIR |grep -v 'current' |tail -n +7`
do
    echo "Deleting old directory $PREDIR/$DEL"
    rm -rf $PREDIR/$DEL
done

/usr/sbin/service sks stop
sleep 2
if [ `ps -eaf |grep "sks " |grep -v 'grep sks' |wc -l` == "0" ]; then
    mkdir -p $INDIR
    chown -R $USER:$USER $INDIR
    cd $INDIR

    rm -rf $OUTDIR && mkdir -p $OUTDIR && \
    chown -R $USER:$USER $PREDIR && \
    sudo -u $USER /usr/local/bin/sks dump $COUNT $OUTDIR/ sks-dump

    if [ `ps -eaf |grep "sks " |grep -v 'grep sks' |wc -l` == 0 ]; then
        chown -R $USER:$USER $INDIR
        /usr/sbin/service sks start
    else
        echo "Unable to start SKS since it was already running."
        exit 1
    fi

    cd $PREDIR/
    if [ ! -d current ]; then
        mkdir current
    fi
    umount current
    mount --bind $OUTDIR current
else
    echo "Unable run backup, SKS is still running."
    exit 1
fi

SIZE=`du -shc $OUTDIR |grep 'total' |awk '{ print $1 }'`
DCOUNT=`grep "#Key-Count" $OUTDIR/metadata-sks-dump.txt |awk '{ print $2 }'`
FILES=`grep "#Files-Count" $OUTDIR/metadata-sks-dump.txt |awk '{ print $2 }'`
echo "This is the PGP key server dump from keyserver.mattrude.com created: `date -u`

On a linux/unix system, you may download this directory via the following command:

wget -c -r -p -e robots=off --timestamping --level=1 --cut-dirs=3 --no-host-directories http://keyserver.mattrude.com/dump/current/

These files were created with the following command: sks dump $COUNT $SKSDATE/ sks-dump

The current archive size is approximately $SIZE, holding $DCOUNT keys in $FILES files.

If you would like to peer with this server, please send an email to <matt@mattrude.com>.

For more information on importing keys from dump files, please see http://keyserver.mattrude.com/guides/building-server/" > $OUTDIR/README.txt

chown -R $USER:$USER $PREDIR
{% endhighlight %}
