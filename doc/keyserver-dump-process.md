---
layout: default
title: Keyserver Dump Process
permalink: /guides/dump-process/
---

## SKS Key Server Dump Process

This script is intended to be ran from cron, since sks requires the db process to be stopped before a dump can be started.  

    0 0 * * * /usr/local/bin/sks-dump-script.sh &

### Key Server Dump Script

{% highlight bash %}
#!/bin/bash

# This script will stop the sks server, dump its contents to
# the $PREDIR, then restart the sks server.
#
# Matt Rude <matt@mattrude.com>  PGP: 0xDD23BF73
# URL: http://keyserver.mattrude.com/guides/dump-process/

SKSDATE=`date +%Y-%m-%d`
USER="debian-sks"
INDIR="/var/lib/sks"
PREDIR="/external/sks-dump"
OUTDIR="$PREDIR/$SKSDATE"

for DEL in `ls -1t $PREDIR |grep -v 'current' |tail -n +8`
do
    echo "Deleting old directory $PREDIR/$DEL"
    rm -rf $PREDIR/$DEL
done

/usr/sbin/service sks stop
if [ `ps -eaf |grep "sks " |grep -v 'grep sks' |wc -l` == "0" ]; then
    mkdir -p $INDIR
    chown -R $USER:$USER $INDIR
    cd $INDIR

    rm -rf $OUTDIR && mkdir -p $OUTDIR && \
    chown -R $USER:$USER $PREDIR && \
    sudo -u $USER /usr/local/bin/sks dump 15000 $OUTDIR/ sks-dump

    if [ `ps -eaf |grep "sks " |grep -v 'grep sks' |wc -l` == 0 ]; then
        chown -R $USER:$USER $INDIR
        /usr/sbin/service sks start
    else
        echo "Unable to start SKS since it was already running."
        exit 1
    fi

    cd $PREDIR/
    rm -f current
    ln -s $OUTDIR current
else
    echo "Unable run backup, SKS is still running."
    exit 1
fi

SIZE=`du -shc $OUTDIR |grep 'total' |awk '{ print $1 }'`
echo "This is the PGP key server dump from keyserver.mattrude.com created: `date -u`

On a linux/unix system, you may download this directory via the following command:

wget -c -r -p -e robots=off --timestamping --level=1 --cut-dirs=3 --no-host-directories http://keyserver.mattrude.com/dump/current/

These files were created with the following command: sks dump 15000 $SKSDATE/ sks-dump

The current archive size is approximately: $SIZE

For more information on importing keys from dump files, please see http://keyserver.mattrude.com/guides/building-server/" > $OUTDIR/README.txt

chown -R $USER:$USER $PREDIR
{% endhighlight %}
