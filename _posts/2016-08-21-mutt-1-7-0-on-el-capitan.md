---
title: "Mutt 1.7.0 on El Capitan"
categories:
  - productivity
tags:
  - mutt
  - email
  - gpg
---
This is an update to my [one year old post](http://www.lucianofiandesio.com/getting-started-with-mutt-on-osx) about mutt and OS X.
On August 2016, a [new version](https://marc.info/?l=mutt-users&amp;m=147154326009383) of mutt was released. This version includes the sidebar patch and the trash folder patch, so no need to mess around with patches any longer.
Here are my notes to get started with the new version.

If you have previously installed mutt via Homebrew, remove it.

```
brew uninstall mutt
```

Download mutt and untar the archive.

```
wget ftp://ftp.mutt.org/pub/mutt/mutt-1.7.0.tar.gz
tar xopf mutt-1.7.0.tar.gz
```

Configure and build.

```
export CPPFLAGS=-I/usr/local/opt/openssl/include
./configure --enable-imap --enable-smtp --enable-sidebar --enable-gpgme --with-regex  --enable-locales-fix --with-ssl --with-sasl --with-gss --enable-hcache --with-tokyocabinet

make

make install
```

I had to link to my (brew based) openssl installation folder for the ```--with-ssl``` flag to work. You also need to install Tokyo Cabinet for the header cache flag to work (```brew install tokyo-cabinet```).

That should be it. 
Do not forget to set the ```trash``` option in your .muttrc file. [Mine](https://github.com/luciano-fiandesio/dotfiles/blob/master/.muttrc#L35) is configured like so: 

```
set trash=imaps://$my_server/INBOX.Trash
```

For additional information on how to configure mutt on OS X, take a look at my [previous](http://www.lucianofiandesio.com/getting-started-with-mutt-on-osx) post.
