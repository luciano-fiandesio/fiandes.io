---
title: "Notes on self-hosting essential apps"
categories:
  - selfhosted
tags:
  - filesharing
  - wiki
  - dav
---

These notes contain a relatively simple guide to set-up a fresh Ubuntu server and self-host a number of (_what I consider_) essential apps.

There are plenty of reasons you want to self-host applications (privacy, who owns your data, your data being stolen, etc.). On the other hand, self-hosting forces you to be very proactive about your data, chiefly:

- establish a rock-solid backup policy 
- keep your server secure

Both back-up and security are not easy feat to get right, so self-hosting requires a certain degree of discipline. Having said that, with a relatively small time-investment, I have never suffered any major (or minor) data-loss or data breach.

I started to look for a self-hosted alternative to Dropbox back in 2013, mostly out of privacy concerns, and I continued to add more self-hosted applications to my server (~~a 4GB RAM box hosted with [Hetzner][1]~~). At this point (end of 2016), my digital backbone is almost completely self-hosted, except for an e-mail server, which I happily leave to [Fastmail][2].

The configuration process described here is based on Ubuntu 16.04 LTS and does install and configure the following applications:

- [Resilio Sync][3] (formerly known as BitTorrent Sync)
- [Baïkal][4] Baïkal (Cal and CardDAV server)
- [Tiny Tiny RSS][5]
- [DokuWiki][6]
- [Piwigo][7] (photo gallery)

All the web-facing applications use HTTPS (thanks [Let's Encrypt!][18]).

It is assumed that the server IP address is associated to a DNS entry and that all the applications have a sub domain, like so:

	- btsync.mydomain.com
	- dav.mydomain.com
	- wiki.mydomain.com

... and so on.

There is an incredible number of self-hosted applications which can be used to replace closed-source, paid applications. A good starting point is [this][19] 'awesome' list and [this][20] sub-reddit. 

## OS

I assume that Ubuntu 16.04 LTS is installed on a new server and the logged in user is `root`.

The very first commands to run will update the package repositories and the system.

```
apt update && sudo apt upgrade
```

Change the root password:

```
passw
```

Setup a login user, with sudo permission

```
adduser youruser
gpasswd -a youruser sudo
```

## Hardening

I don't want to be hacked, rooted or my server to turn into a remotely controlled zombie. Harden.Your.Server.
I have been using this excellent [collection][8] of security recomendations to harden my box, and you should do the same.

The crucial bits are documented here. I **warmly** recommend to read the above guide and decide for yourself how paranoid you want to be when hardening your server. 

### Firewall

```
# install UFW (Uncomplicated Firewall)
sudo apt-get install ufw

# allow ssh and apache 80/443
sudo ufw allow ssh
sudo ufw allow "Apache Full"

# enable the firewall
sudo ufw enable
```

### Harden SSH

Just follow this [setup][9].
It shows how to set up public key authentication for the new user and disable password authentication.

Be extra careful when editing the server's SSL configuration. It is scary simple to get locked out of a server.

### Secure shared memory

Shared memory can be used in an attack against a running service, apache2 or httpd for example. More info [here][10].
To secure the shared memory:

```
sudo vim /etc/fstab
```

Add the following line:

```
tmpfs     /run/shm     tmpfs     defaults,noexec,nosuid     0     0
```

Save and reboot.

### Disable SSL v3

Here's [why][11] you want to disable SSL v3. 

TLTR; it's broken and vulnerable.

```
sudo vim /etc/apache2/mods-available/ssl.conf
```

Change the line:

```
SSLProtocol all -SSLv3
```

To:

```
SSLProtocol all -SSLv2 -SSLv3
```

Restart apache:

```
sudo apache2ctl configtest && sudo service apache2 restart 
```

### Setup fail2ban

[Fail2ban][12] temporarily rejects IP addresses that are trying to do something dodgy with your server.
A classical example is a client try to brute force the SSH password. 
The default Fail2ban configuration only protectes SSH, but it's (relatively) easy to configure it to add more checks (eg. apache HTTP server)

```
sudo apt-get install fail2ban -y
```

## Installing applications

### LAMP Stack

Before installing any application, we want to install Apache2, MySQL and PHP.

You can get away with a one liner:

```
sudo apt-get install apache2 mysql-server php libapache2-mod-php php-mcrypt php-mysql
```

But you really want to follow this thorough [guide][13], from the guys @[DigitalOcean][14] . 

### Resilio Sync

[Resilio Sync][3] is the new incarnation of (the formerly known) BitTorrent Sync. In June, BitTorrent inc. [announced][15] that the Sync product was going to be moved into a new company, called Resilio.
This news made me a bit nervous, but so far it seems that the new company is delivering. They recently released version 2.4 of the Sync client.

Resilio Sync is essentially a Dropbox replacement: you should be aware that it is a closed-source software. There are open-source alternatives, such as [SyncThing][24] or [Seafile][25]. I consider BitTorrent Sync (now Resilio Sync) the easiest to setup and to run and the more reliable.

![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/86ac383c-41e3-4c22-bc48-9b548381dc8f/resilio_large.png)

Before installing the Sync client, create the folder where the synchronized files will be stored. 

``` 
# change your-user with the directory name of the user created before
sudo apt-get install acl 
mkdir /home/your-user/btsync_share/

sudo setfacl -R -m "u:rslsync:rwx" /home/your-user/btsync_share/
```

To install Resilio Sync, start by creating a new `apt` source file

```
echo 'deb http://linux-packages.getsync.com/resilio-sync/deb resilio-sync non-free' | sudo tee --append /etc/apt/sources.list.d/resilio-sync.list
```

Add public key with the following command:

```
wget -qO - https://linux-packages.getsync.com/resilio-sync/key.asc | sudo apt-key add -
```

Install

```
sudo apt-get update && sudo apt-get install resilio-sync
```

Start Resilio Sync

```
sudo service resilio-sync start
```

The Linux version of Resilio Sync has a web-based UI available at 127.0.0.1:8888. The web UI is not accessible from the Internet, so we need to set up a reverse proxy.

First enable the Apache modules required for the reverse proxy to work:
 
```
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod rewrite
sudo a2enmod deflate
sudo a2enmod headers
sudo a2enmod proxy_balancer
sudo a2enmod proxy_connect
sudo a2enmod proxy_html
```

Create the Apache configuration for the Resilio Sync UI.
Here, I assume that you have a domain name associated to your server and that each application will correspond to a sub-domain

Let's pretend that the domain name is `awesomehosted.com`

Create a new Apache configuration file

```
sudo touch /etc/apache2/sites-available/btsync.awesomehosted.com.conf
```

Copy the following text to the new file

{% gist 38eac0183e1914d18015f36267d1fc2c btsync.awesomehosted.com.conf %}

Enable the configuration and restart Apache

```
sudo a2ensite btsync.awesomehosted.com.conf
sudo service apache2 restart
```

If the sub domains are already configured and propagated, you should be able to access the Resilio Sync console

```
http://btsync.awesomehosted.com
```

In case you haven't configured the A records yet, just add an entry to the `/etc/hosts` of the computer testing the connection

```
[server ip] btsync.awesomehosted.com
```

### Baïkal

[Baïkal][4] is an excellent CalDav and CardDav server. I have been using it for a couple of years, and it totally replaced any other cloud service for syncing contacts and calendar (Apple Cloud, etc.).
It is very stable and (kind of) easy to setup. The data are stored on SQLite, but for large dataset it is reccommended to use MySQL. I use SQLite because it's easy to back up :)


![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/92f752d7-b589-46e7-ab23-d7b8addc5af3/baikal_large.jpg)

```
# install the dependencies
sudo apt-get install php7.0-sqlite3 php-xml php7.0-mbstring unzip

# fetch baikal
cd /var/www
sudo wget https://github.com/fruux/Baikal/releases/download/0.4.6/baikal-0.4.6.zip
sudo unzip baikal-0.4.6.zip 

# rename the baikal folder 
sudo mv baikal dav.awesomehosted.com
sudo rm baikal-0.4.6.zip

# fix permissions
sudo chown -R www-data:www-data dav.awesomehosted.com
```

Create a new Apache configuration file

```
sudo touch /etc/apache2/sites-available/dav.awesomehosted.com.conf
```

Copy the following text to the new file

{% gist a4e189f18ddc8d8e450a8893fcd06c0f dav.awesomehosted.com.conf %}

And enable the configuration

```
sudo a2ensite dav.awesomehosted.com.conf
sudo service apache2 restart
```

Hit `dav.awesomehosted.com` on your browser and you should access the configuration page. 
Choose an admin password and pick MySQL or SQLite. If you choose MySQL, you'll have to create a database first.
I don't bother creating a MySQL db for Baikal. I have around 1000 contacts and a relatively low number of entries in my calendars and never had a problem. Bonus: SQLite is easier to backup!

As an Android user, I use [CardDAV-Sync][16] to syncronize my contacts and [CalDav-Sync][17] for the calendar syncronization.

Once you have created a new user (eg. johndoe), the url for the contacts is: `http://dav.awesomehosted.com/card.php/addressbooks/johndoe/default/`.
The url for the calendar is: `http://dav.awesomehosted.com/cal.php/calendars/johndoe/default/`.

### Tiny Tiny RSS

[Tiny Tiny RSS][5] (tt-rss for brevity) is a marvellous feed reader, which I use daily. There are [lots of self-hosted][21] feed readers out there: I have tried few of them, and I settled with tt-rss. The set-up process is similar to the one used for the other applications.

![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/afee07bf-936d-4f97-bd9b-2f46706544bb/tiny-tiny-rss_large.png)

Install some required PHP dependencies.

```
sudo apt-get install php-curl php7.0-mbstring php-xml
sudo service apache2 restart
```

Clone tt-rss

```
cd /var/www

sudo git clone https://tt-rss.org/git/tt-rss.git tt-rss

sudo mv tt-rss rss.awesomehosted.com

sudo chown -R www-data:www-data rss.awesomehosted.com

```

Create a new Apache configuration file

```
sudo touch /etc/apache2/sites-available/rss.awesomehosted.com.conf
```

Copy the following text to the new file

{% gist e4e14a0234f71cbe4ecbcffa31cdc595 rss.awesomehosted.com.conf %}

And enable the configuration

```
sudo a2ensite rss.awesomehosted.com.conf
sudo service apache2 restart
```

Create the Tiny Tiny RSS database. Don't forget to keep the password in a safe place.

```
mysql -u root -p

mysql&gt; create database ttRssDB;
mysql&gt; grant all on ttRssDB.* to ttRss@localhost identified by 'password';
mysql&gt; flush privileges;
mysql&gt; \q
```

The first time you hit the url 'http://rss.awesomehosted.com', a set-up page is displayed. Admittedly, the setup process of tt-rss it's a bit quirky.
Fill the configuration form with the information provided during the db creation stage.
Click on the "Test Configuration" button to double-check you can access the database and all required libraries are present. Finally, select "Initialize Database". Now you should be presented with a textbox containing the php configuration code required to run tt-rss. Press the "Install Configution" button. This will create a `config.php` file in the root of the main application directory.

You should now be able to access tt-rss. The default username and password is: `admin/password`. Don't forget to create a new user and change the administrative password!

The last step required for a fully functional feed reader is creating a cronjob to refresh the feeds at a specified interval.

```
crontab -e

*/30 * * * \* /usr/bin/php /var/www/tt-rss/update.php --feeds --quiet
```

### Docuwiki

My wiki is probably the most important part of my digital life. I keep track of books I read or I want to read, movies, a '90s style weblog, ideas and notes. Before starting a personal wiki, I was an heavy user of Evernote. The problem with a note-style knowledge management system, such has Evernote, is that it quickly becomes a huge knowledge sink and the truly important bits of information that I want to retain were lost in the noise of the less important data.

A wiki instead forces me to curate the information that I want to preserve. A wiki is akin to having a paper notebook. It is slower, but it forces me to think about the actual bit of data that I'm working on.

![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/7cf8e211-9ed9-47d0-952a-647c7022bd37/dokuwiki_large.png)

[Docuwiki][6] is a very simple wiki platform with a couple of advantages over other wikis: it has a large plugin ecosystem and it is file-based, so super-easy to back-up. No database, less attack-surface. The installation is also straightforward.

```
cd ~

# download the latest version of docuwiki
wget http://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
tar xvf dokuwiki-stable.tgz
sudo mv dokuwiki-2016-06-26a/ /var/www/wiki

# setup folder permissions
sudo chown www-data:www-data -R /var/www/wiki/

# create apache conf file
touch /etc/apache2/sites-available/wiki.awesomehosted.com.conf
```

Copy the following configuration snippet to the new file

{% gist d9ce4a2c15484f347c623f0c7527d71b wiki.awesomehosted.com.conf %}

Restart Apache and test the installation by browsing to `http://wiki.awesomehosted.com`.

### Piwigo

[Piwigo][7] is a photo gallery web-application. I use it to share photo galleries with my family. I switched from the very stylish-looking [Lychee][22] to Piwigo because Lychee doesn't support custom ordering of photos in a gallery.

![Silvrback blog image](https://silvrback.s3.amazonaws.com/uploads/0693fb47-dd78-464f-89bc-c5f6b563571d/piwigo_large.jpg)

Piwigo setup is straightforward. Start by creating a database. Don't forget to change the password.

```
mysql -u root -p

CREATE DATABASE piwigo;

CREATE USER piwigouser@localhost IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON piwigo.* TO piwigouser@localhost;

FLUSH PRIVILEGES;

exit
```

```
cd ~

sudo apt-get install unzip php-gd

wget "http://piwigo.org/download/dlcounter.php?code=latest" -O piwigo-gallery.zip

sudo unzip piwigo-gallery.zip -d /var/www

sudo mv /var/www/piwigo /var/www/gallery.awesomehosted.nl

sudo chown www-data: -R /var/www/gallery.cryptonomicon.nl/

```

Create the Apache config file

```
sudo touch /etc/apache2/sites-available/gallery.awesomehosted.com.conf
```

Copy the following configuration to `gallery.awesomehosted.com.conf`

{% gist 451c7d1ab3d3257a838560f0ed90b222 gallery.awesomehosted.com.conf %}

Finally, add the following lines to `my.cnf`

```
sudo vim /etc/mysql/my.cnf

[mysqld]
sql-mode=""
```

This is required to avoid a [weird error][23] when changing the order of the photos in the gallery.

## Secure the applications with TLS/SSL

Once all the applications are installed, configured and running on port 80 within Apache 2, it's time to install a TLS/SSL certificate in order to encrypt the traffic between client and server. The obvious choice these days is to use [Let's Encrypt][18]

Let's Encrypt is a new SSL authority that provides free SSL certificates and a super-simple command line tool to streamline the creation and renewal of certificates.

Let's start by installing the command line tool.

```
sudo apt-get install python-letsencrypt-apache
```

Modify the Apache 2 default virtual host file, `000-default.conf` so that the domain name of your server is properly defined.

```
sudo vim /etc/apache2/sites-available/000-default.conf
```

Make sure that the `ServerName` and `ServerAlias` are pointing to the correct domain name - let's continue using the fictional `awesomehosted.com` domain name:

```
ServerName awesomehosted.com
ServerAlias www.awesomehosted.com
ServerAdmin webmaster@localhost
DocumentRoot /var/www/awesomehosted
```

I like to configure the root directory with my own index.html, hence `DocumentRoot` pointing to a non default folder.

Finally, execute the command that will magically install and configure the SSL certificate for the main domain and the subdomains.

```
letsencrypt --apache -d awesomehosted.com -d www.awesomehosted.com -d btsync.awesomehosted.com -d dav.awesomehosted.com -d rss.awesomehosted.com -d wiki.awesomehosted.com -d gallery.awesomehosted.com
```

Note that his command will **fail** if the DNS settings for the domains and subdomains are not properly configured and propagated.

Stay tuned for the second part of these notes, concerning my backup strategy.


[1]:	https://www.hetzner.de/en/
[2]:	https://www.fastmail.com/
[3]:	https://www.resilio.com/individuals/
[4]:	http://sabre.io/baikal/
[5]:	https://tt-rss.org/
[6]:	https://www.dokuwiki.org/dokuwiki#
[7]:	http://piwigo.org/
[8]:	https://www.thefanclub.co.za/how-to/how-secure-ubuntu-1604-lts-server-part-1-basics
[9]:	https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04
[10]:	https://help.ubuntu.com/community/StricterDefaults
[11]:	http://disablessl3.com/
[12]:	http://www.fail2ban.org/wiki/index.php/Main_Page
[13]:	https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04
[14]:	https://www.digitalocean.com/
[15]:	http://variety.com/2016/digital/news/bittorrent-sync-spinoff-resilio-1201787793/
[16]:	https://play.google.com/store/apps/details?id=org.dmfs.carddav.Sync&amp;hl=en
[17]:	https://play.google.com/store/apps/details?id=org.dmfs.caldav.lib&amp;hl=en
[18]:   https://letsencrypt.org/
[19]:   https://github.com/Kickball/awesome-selfhosted
[20]:   https://www.reddit.com/r/selfhosted/
[21]:   https://github.com/Kickball/awesome-selfhosted#feed-readers
[22]:   https://lychee.electerious.com/
[23]:   https://github.com/Piwigo/Piwigo/issues/376
[24]:   https://syncthing.net/
[25]:   https://www.seafile.com/en/home/