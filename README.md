# PGP Keyserver Site Source

[![Build Status](https://travis-ci.org/mattrude/pgpkeyserver.svg?branch=master)](https://travis-ci.org/mattrude/pgpkeyserver) [![GitHub license](https://img.shields.io/github/license/mattrude/pgpkeyserver.svg)](https://github.com/mattrude/pgpkeyserver/blob/master/LICENSE) [![GitHub tag](https://img.shields.io/github/tag/mattrude/pgpkeyserver.svg)](https://github.com/mattrude/pgpkeyserver/tags) [![GitHub commits since](https://img.shields.io/github/commits-since/mattrude/pgpkeyserver/v0.2.0.svg)](https://github.com/mattrude/pgpkeyserver/compare/v0.1.1...master) [![Open Issues](https://img.shields.io/github/issues-raw/mattrude/pgpkeyserver.svg)](https://github.com/mattrude/pgpkeyserver/issues) [![Maintenance](https://img.shields.io/maintenance/yes/2017.svg)](http://github.com/mattrude/pgpkeyserver)

This repsitory holds the source for a [SKS PGP Key Server](https://sks-keyservers.net/) website similer to [keyserver.mattrude.com](http://keyserver.mattrude.com).  This is only the HTML source code for the supporting documention site for the PGP server, not the PGP Keyserver software used on the server.

## Jekyll

[Jekyll](http://jekyllrb.com/) is a static site generator built in [Ruby on Rails](http://rubyonrails.org/). With this approach, you are able to build a high power build site, using a low power, very stable webserver running a static site.

Jekyll is not needed on the webserver, updates must be done by a build system, from this source repository, then sent to the webserver.

### Updating the site

First change into the source directory of the site, once in, update via

    jekyll build

A simple script would be

    rm -rf /var/src/pgpkeyserver && mkdir -p /var/src/ && \
    git clone git@github.com:mattrude/pgpkeyserver.git /var/src/pgpkeyserver -q && \
    cd /var/src/pgpkeyserver && jekyll build -q && rm -rf /var/src/pgpkeyserver

## Installing the server

### Nginx Configuration

    #----------------------------------------------------------------------
    # OpenPGP Public SKS Key Server
    #----------------------------------------------------------------------

    # keyserver.mattrude.com
    server {
        listen 80;
        listen [::]:80;
        listen <set your IP>11371;
        listen [set your IPv6 IP]:11371;
        server_name keyserver.mattrude.com;
        server_name pool.sks-keyservers.net;
        server_name *.pool.sks-keyservers.net;
        server_name pgp.mit.edu;
        server_name pgp.ipfire.org;
        server_name keys.gnupg.net;
        root /var/www/keyserver.mattrude.com;

        rewrite ^/stats /pks/lookup?op=stats;
        rewrite ^/s/(.*) /pks/lookup?search=$1;
        rewrite ^/search/(.*) /pks/lookup?search=$1;
        rewrite ^/g/(.*) /pks/lookup?op=get&search=$1;
        rewrite ^/get/(.*) /pks/lookup?op=get&search=$1;
        rewrite ^/k/(.*) /pks/lookup?op=get&search=$1;
        rewrite ^/key/(.*) /pks/lookup?op=get&search=$1;
        rewrite ^/d/(.*) /pks/lookup?op=get&options=mr&search=$1;
        rewrite ^/download/(.*) /pks/lookup?op=get&options=mr&search=$1;

        location /dump {
            alias /var/www/sks-dump;
            autoindex on;
            autoindex_exact_size off;
            add_before_body /nginx-before.txt;
            add_after_body /nginx-after.txt;
        }

        location /pks {
            proxy_pass         http://127.0.0.1:11371;
            proxy_pass_header  Server;
            add_header         Via "1.1 keyserver.mattrude.com:11371 (nginx)";
            proxy_ignore_client_abort on;
            client_max_body_size 8m;
        }
    }


### Installing Jekyll
Since Jekyll only needs to be installed on your build system. Below are a few quick how-to's how setting up your build system.

#### Installing Ruby on Ubuntu
On Ubuntu 14.04 LTS, you first need ruby installed on your setup, we will also install the development kit.

    apt-get update; apt-get install -y git g++ ruby ruby-dev

Next install the needed gems and Jekyll

    gem install rails
    gem install rouge
    gem install kramdown
    gem install therubyracer
    gem install jekyll
    gem install jekyll-press
    gem install jekyll-sitemap
    gem install jekyll-less
    gem install jekyll-redirect-from
    gem install jekyll-last-modified-at

Now you may use Jekyll to build the site, using the source provided in this repository.

#### Installing Ruby on Windows
First start out by downloading the current production version of the [Ruby Installer](http://rubyinstaller.org/downloads/) for windows.

##### Installing the Ruby Development Kit
After installing Ruby via the [Ruby Installer](http://rubyinstaller.org/downloads/) talked about above, you must now download the Development Kit.

1. Download the Development Kit from http://rubyinstaller.org/downloads/
1. Extract the contact into a location easy accessible to your command prompt.
1. Open a command prompt, change into the directory that you extracted the content of the Development Kit to and run the command: `rake devkit sfx=1`.

## License

                  GNU GENERAL PUBLIC LICENSE
                    Version 3, 29 June 2007

    OpenPGP Key Server Website for keyserver.mattrude.com
    Copyright (C) 2012-2015 Matt Rude <matt@mattrude.com>

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
