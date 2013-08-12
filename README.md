nginx-extended-statistics-path
==============================

Nginx extended statistics patch for nginx 0.7.67. This patch add ability to check count and total size of requests to certain vhosts 

Build this patch on Debian 7 Squeeze:
=======
Author: Alexander Rebrikov

Build this patch on Debian 

```bash
# Update repository content
apt-get update
# Build packages needed for building Debian packages
apt-get install -y dpkg-dev devscripts build-essential fakeroot
# Install dependency's for building nginx
apt-get build-dep -y nginx
# Additional dependency for nginx 0.7
apt-get install -y dpatch libgeoip-dev
# Go to safe directory for building packages
cd /usr/src
apt-get source nginx

# make patch
dpkg-source -x nginx_0.7.67-3+squeeze3.dsc

export VERSION=0.7.67
cd nginx-$VERSION

# make fullstats patch
wget https://raw.github.com/FastVPSEestiOu/nginx-extended-statistics-path/master/nginx_stats.patch
patch -p1 < nginx_stats.patch


# Build nginx
debuild -us -uc # -us unsigned source, -uc unsigned changes

# Install nginx:
dpkg -i /usr/src/nginx_0.7.67-3+squeeze3_amd64.deb 

# Lock package for preventing upgrade to non patched version from repo
echo "nginx hold" | dpkg --set-selections
```


After that, you need add special location to any server for getting nginx statistics:

```bash
location /nginx_status {
    full_status on; 
} 
```


```bash
After that, you can see detailed statistics for every virtual host on this server
vihost				requests		active_time(msec)		traf
client1.shared.ru			18			0				6355
samedomain.com				22			2			8713
```

# Optional, don't needed for you if u don't knew what is it

```bash
# Optional, don't needed for you if u don't knew what is it 
# enable eval module http://www.grid.net.ru/nginx/eval.en.html 
#cd modules
#wget http://fastvps.googlecode.com/svn/trunk/packages/vkholodkov-nginx-eval-module-1.0.2-0-ge85e11e.tar.gz
#tar -xf vkholodkov-nginx-eval-module-1.0.2-0-ge85e11e.tar.gz
#rm -f vkholodkov-nginx-eval-module-1.0.2-0-ge85e11e.tar.gz
#cd ..

# Now fully automated
#sed -i 's#--with-mail_ssl_module \\#--with-mail_ssl_module \\\n\t    --add-module=$(CURDIR)/modules/vkholodkov-nginx-eval-module-e85e11e \\#' debian/rules 


```
