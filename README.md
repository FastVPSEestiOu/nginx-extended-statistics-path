nginx-extended-statistics-path
==============================

Nginx extended statistics patch for nginx 0.7. This patch add ability to check count and total size of requests to certain vhosts 

Build this patch on Debian 

```bash
#!/bin/bash

#
# By https://progress.fastvps.ru/view.php?id=543
#

# get links from: http://packages.debian.org/squeeze/nginx
apt-get update
cd /usr/src
wget http://ftp.de.debian.org/debian/pool/main/n/nginx/nginx_0.7.67-3.dsc
wget http://ftp.de.debian.org/debian/pool/main/n/nginx/nginx_0.7.67.orig.tar.gz
wget http://ftp.de.debian.org/debian/pool/main/n/nginx/nginx_0.7.67-3.debian.tar.gz

# install build soft
apt-get install -y dpkg-dev devscripts build-essential fakeroot

# make patch
dpkg-source -x nginx_0.7.67-3.dsc

# install dependency for 0.6
apt-get build-dep -y nginx

# install dependency for 0.7
apt-get install -y dpatch libgeoip-dev

export VERSION=0.7.67
cd nginx-$VERSION

# change standards version
sed -i 's/\(Standards-Version: \).*/\13.8.0/g' debian/control 

# make fullstats patch
wget http://.../nginx_stats.patch
patch -p1 < nginx_stats.patch

# enable eval module http://www.grid.net.ru/nginx/eval.en.html 
cd modules
wget http://fastvps.googlecode.com/svn/trunk/packages/vkholodkov-nginx-eval-module-1.0.2-0-ge85e11e.tar.gz
tar -xf vkholodkov-nginx-eval-module-1.0.2-0-ge85e11e.tar.gz
rm -f vkholodkov-nginx-eval-module-1.0.2-0-ge85e11e.tar.gz
cd ..

# DEPREACTED: Custom part
# enable eval module build
# vi debian/rules
# and after --add-module=$(CURDIR)/modules/nginx-upstream-fair \
# insert:
# --add-module=$(CURDIR)/modules/vkholodkov-nginx-eval-module-e85e11e \

# Now fully automated
sed -i 's#--with-mail_ssl_module \\#--with-mail_ssl_module \\\n\t    --add-module=$(CURDIR)/modules/vkholodkov-nginx-eval-module-e85e11e \\#' debian/rules 

# build nginx
debuild -us -uc # -us unsigned source, -uc unsigned changes


```
