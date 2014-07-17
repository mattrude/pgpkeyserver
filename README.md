# PGP Keyserver Site Source

This repsitory holds the source for the PGP Key Server site at [keyserver.mattrude.com](http://keyserver.mattrude.com).

## Jekyll

### Updating site

    jekyll build

## Installing Jekyll
Since Jekyll only needs to be installed on your build system. Below are a few quick how-to's how setting up your build system.

### Installing Jekyll on Ubuntu
On Ubuntu 14.04 LTS, you first need ruby installed on your setup, we will also install the dev packets.

    apt-get install git ruby ruby-dev ruby-dir

Next install the needed gems and Jekyll

    gem install rails
    gem install kramdown
    gem install therubyracer
    gem install jekyll
    gem install jekyll-sitemap
    gem install jekyll-less

Now you may use Jekyll to build the site, using the source provided in this repository.

### Installing Jekyll on Windows
### Installing Jekyll on OSX

## License

                  GNU GENERAL PUBLIC LICENSE
                     Version 2, June 1991
                     
    Copyright (C) 2012-2014 Matt Rude <matt@mattrude.com>
     
    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.
    
    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.
