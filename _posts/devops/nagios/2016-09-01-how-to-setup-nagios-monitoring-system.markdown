---
layout: post
title: How to Setup Nagios Monitoring System
author:
modified:
comments: true
categories: devops/nagios
excerpt: "Nagios is an enterprise class, open source software that can be used for network and infrastructure monitoring. Using Nagios, we can monitor servers, switches, applications and services etc. It alerts the System Administrator when something goes wrong and also alerts back when the issues have been rectified."
tags: [Devops, Nagios, NRPE, Ubuntu]
image:
  url: https://cloud.githubusercontent.com/assets/1223371/18166139/0917267c-7067-11e6-8efb-58950b52fab4.jpg
  alt: How to Setup Nagios Monitoring System
  title: How to Setup Nagios Monitoring System
  feature:
date: 2016-09-01T17:01:36+05:30
---


{% include _toc.html %}

<img src="{{ page.image.url }}" alt="{{ page.image.alt }}" title="{{ page.image.title }}">

### Prerequisites

{% highlight bash %}
# Ubuntu/Debian
$ sudo apt-get install wget build-essential openssl libssl-dev apache2 php apache2-mod-php7.0 php-gd libgd-dev unzip apache2-utils
{% endhighlight %}

### Adding the Nagios User and Group
{% highlight bash %}
$ useradd nagios
$ groupadd nagcmd
$ usermod -a -G nagcmd nagios
$ usermod -a -G nagios,nagcmd www-data
{% endhighlight %}


### Download Nagios Core, Nagios Plugins & NRPE Tarballs
{% highlight bash %}
$ cd /tmp
$ wget http://prdownloads.sourceforge.net/sourceforge/nagios/nagios-4.2.0.tar.gz
$ wget http://nagios-plugins.org/download/nagios-plugins-2.1.2.tar.gz
$ wget https://github.com/NagiosEnterprises/nrpe/archive/3.0.tar.gz
{% endhighlight %}

#### Extract Nagios Core, Nagios Plugins & NRPE Tarballs
{% highlight bash %}
$ tar zxvf nagios-4.2.0.tar.gz
$ tar zxvf nagios-plugins-2.1.2.tar.gz
$ tar zxvf 3.0.tar.gz
{% endhighlight %}


### Nagios Core Installation

{% highlight bash %}
$ cd nagios-4.2.0
$ ./configure  --with-nagios-group=nagios --with-command-group=nagcmd --with-httpd-conf=/etc/apache2/sites-available/

$ make all
$ make install
$ make install-init
$ make install-config
$ make install-commandmode
$ make install-webconf
{% endhighlight %}

#### Copy Nagios Core Files

{% highlight bash %}
$ cp -R contrib/eventhandlers/ /usr/local/nagios/libexec/
$ chown -R nagios:nagios /usr/local/nagios/libexec/eventhandlers
{% endhighlight %}

### Setup Apache

{% highlight bash %}
$ sudo a2ensite nagios
$ sudo a2enmod rewrite cgi
$ htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
{% endhighlight %}

### Nagios Plugins Installation

{% highlight bash %}
$ cd /tmp/nagios-plugins-2.1.2
$ ./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl --with-ping-command=ping
$ make
$ make install
{% endhighlight %}


### Setup NRPE
{% highlight bash %}
$ cd /tmp/nrpe-3.0/
$ ./configure
$ make all
$ make install
$ make install-config
$ make install-init
{% endhighlight %}

### Restart Services

{% highlight bash %}
$ service apache2 restart
$ service nagios restart
$ service nrpe restart
{% endhighlight %}

### Enable Services
{% highlight bash %}
$ sudo update-rc.d nagios defaults
{% endhighlight %}

### Check NRPE & Nagios

{% highlight bash %}
$ /usr/local/nagios/libexec/check_nrpe -H 127.0.0.1
NRPE vnrpe-3.0

$ /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
{% endhighlight %}

### Nagios Web Interface

* After correctly following the procedures you should now be able to access your Nagios Core installation from a
web browser.
* Simply use the following: <a href="http://1.1.1.1/nagios">http://1.1.1.1/nagios</a>
* Log in with the credentials you chose when adding the nagiosadmin user to the htpasswd.users file.
