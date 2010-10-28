# Project:   Todos

## Copyright: ©2010 Ido Ran (ido.ran@gmail.com)

SproutCore Todos sample application <https://sproutcore.pbworks.com/Todos%C2%A0Intro> implemented with a CouchDB
backend. The special contribution of this project is the DataSource which knows how to work with CouchDB
documents.

RPK 10/27/10:

### Modifications for running with CouchDB proxied at `/db/` on a Mac:

#### install CouchDB (on OS X):

      $ sudo port install couchdb
      $ sudo dscl localhost
      > cd /Local/Default/Users
      /Local/Default/Users > change couchdb dsAttrTypeNative:home /dev/null /opt/local/var/lib/couchdb
      /Local/Default/Users > change couchdb dsAttrTypeNative:shell /dev/null /bin/bash

      $ sudo chown -R couchdb:couchdb /opt/local/var/lib/couchdb
      $ sudo chown -R couchdb:couchdb /opt/local/var/log/couchdb
      $ sudo chown -R couchdb:couchdb /opt/local/etc/couchdb
      $ sudo launchctl load -w /Library/LaunchDaemons/org.apache.couchdb.plist
     
#### Turn on Web Sharing (Apple Menu > System Preferences > Sharing > Web Sharing)
 
#### if necessary, uncomment the vhosts line in `/private/etc/apache2/httpd.conf' so it looks like the following:
 
    # Virtual hosts
    Include /private/etc/apache2/extra/httpd-vhosts.conf

#### add the following line to the `/etc/hosts`:
 
    127.0.0.1 sc.local

#### add the following to `/private/etc/apache2/extra/httpd-vhosts.conf`:
 
    <VirtualHost *:80>
      ServerAdmin webmaster@localhost
      DocumentRoot "/opt/local/www/dummy"
      ServerName sc.local
      ProxyRequests Off
      KeepAlive Off
      <Proxy *>
        Order deny,allow
        Deny from all
        Allow from 127.0.0.1
      </Proxy>

      ProxyPass /db/ http://127.0.0.1:5984/ nocanon retry=0
      ProxyPassReverse /db/ http://127.0.0.1:5984/
      ProxyPass / http://127.0.0.1:4020/ retry=0
      ProxyPassReverse / http://127.0.0.1:4020/

    </VirtualHost>
     
#### restart apache:

    $ apachectl configtest
    $ sudo apachectl restart
    

The couchdb database should be accessible at <http://sc.local/db/>. You should be able to run the Todos
application by visiting <http://sc.local/todos>.
