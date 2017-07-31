Source from http://blog.arnas.web.id/?p=145 with edits 



The following is an simplified how to install SSL Certificate on zPanel. You can use a self-signed SSL Certificate or buy.

First create ssl directory to easy maintain:
$ mkdir /var/zpanel/hostdata/zadmin/ssl

Then go to ssl directory:
$ cd /var/zpanel/hostdata/zadmin/ssl

Generate private key. If you use a self signed SSL Certificate, you can use 1024 RSA key, but if you buy a certificate like InstantSSL, most of them need at least 2048 bit RSA key.
$ openssl genrsa -des3 -out server.key 2048

Generate CSR (Certificate Signing Request):
$ openssl req -new -key server.key -out server.csr

Remove Passphrase from Key
$ cp server.key server.key.org
$ openssl rsa -in server.key.org -out server.key

If you want to use a self signed, generate a self signed certificate:
$ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

Or if you want to buy a certificate, please submit server.key to a SSL Provider, then you’ll get .crt and .ca-bundle files.

Install the Private Key and Certificate.
Please make sure mod_ssl was installed on your server. If didn’t installed yet, you can install it.
For CentOS:
# yum install mod_ssl
For Ubuntu:
# dpkg -S mod_ssl.so
But if you get this message: apache2.2-common: /usr/lib/apache2/modules/mod_ssl.so
Then you just need to enable it

a2enmod ssl

Go Server Admin – Module Admin – Override a Virtual Host Setting – Select a domain and click Select Vhost:
On port override field, fill: 443
Check ‘Forward Port 80 to Overriden Port’ if you want to always use HTTPS
Fill IP Override with your Server IP Address.
If you use a self-signed certificate, fill custom entry with:
SSLEngine On
SSLCertificateFile /var/zpanel/hostdata/zadmin/ssl/server.crt
SSLCertificateKeyFile /var/zpanel/hostdata/zadmin/ssl/server.key
DocumentRoot "/var/zpanel/hostdata/zadmin/public_html/yourdirectory"

##Use https://whatsmychaincert.com to generate crt chain file

##If you use trusted SSL Certificate, fill custom entry with:
SSLEngine On
SSLCertificateFile /var/zpanel/hostdata/zadmin/ssl/server.crt
SSLCertificateKeyFile /var/zpanel/hostdata/zadmin/ssl/server.key
SSLCertificateChainFile /var/zpanel/hostdata/zadmin/ssl/server.ca-bundle
DocumentRoot "/var/zpanel/hostdata/zadmin/public_html/yourdirectory"

Click Save VHost

Then final step restart apache:
#/etc/init.d/httpd restart
