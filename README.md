- 👋 Hi, I’m @poemo040
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
poemo040/poemo040 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
readme.md echo "# poetry-" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:poemo040/poetry-.git
git push -u origin main
# This configuration has been tested on GitLab 13.8
# Note this config assumes unicorn/puma is listening on default port 8080 and
# gitlab-workhorse is listening on port 8181.
# To make puma listen on port 8080 edit gitlab/config/puma.rb and add the following:
#
# bind 'tcp://127.0.0.1:8080'
#
# To allow gitlab-workhorse to listen on port 8181, edit or create
# /etc/default/gitlab and change or add the following:
#
# gitlab_workhorse_options="-listenUmask 0 -listenNetwork tcp -listenAddr 127.0.0.1:8181 -authBackend http://127.0.0.1:8080"
#

#Module dependencies
# mod_rewrite
# mod_ssl
# mod_proxy
# mod_proxy_http
# mod_headers

# This section is only needed if you want to redirect http traffic to https.
# You can live without it but clients will have to type in https:// to reach gitlab.
<VirtualHost *:80>
  ServerName YOUR_SERVER_FQDN
  ServerSignature Off

  RewriteEngine on
  RewriteCond %{HTTPS} !=on
  RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [NE,R,L]
</VirtualHost>

<VirtualHost *:443>
  SSLEngine on
  #strong encryption ciphers only
  #see ciphers(1) http://www.openssl.org/docs/apps/ciphers.html
  SSLProtocol all -SSLv2
  SSLHonorCipherOrder on
  SSLCipherSuite "ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+3DES:!aNULL:!MD5:!DSS"
  Header add Strict-Transport-Security: "max-age=15768000;includeSubdomains"
  SSLCompression Off
  #CentOS
  SSLCertificateFile /etc/httpd/ssl.crt/YOUR_SERVER_FQDN.crt
  SSLCertificateKeyFile /etc/httpd/ssl.key/YOUR_SERVER_FQDN.key
  SSLCACertificateFile /etc/httpd/ssl.crt/your-ca.crt
  #Debian
  #SSLCertificateFile /etc/ssl/certs/YOUR_SERVER_FQDN.crt
  #SSLCertificateKeyFile /etc/ssl/private/YOUR_SERVER_FQDN.key
  #SSLCACertificateFile /etc/ssl/certs/your-ca.crt
  ServerName YOUR_SERVER_FQDN
  ServerSignature Off
  ProxyPreserveHost On

  # Ensure that encoded slashes are not decoded but left in their encoded state.
  # http://doc.gitlab.com/ce/api/projects.html#get-single-project
  AllowEncodedSlashes NoDecode

  <Location />
    # New authorization commands for apache 2.4 and up
    # http://httpd.apache.org/docs/2.4/upgrading.html#access
    Require all granted

    #Allow forwarding to gitlab-workhorse
    ProxyPassReverse http://127.0.0.1:8181
    ProxyPassReverse http://YOUR_SERVER_FQDN/
  </Location>

  # Apache equivalent of nginx try files
  # http://serverfault.com/questions/290784/what-is-apaches-equivalent-of-nginxs-try-files
  # http://stackoverflow.com/questions/10954516/apache2-proxypass-for-rails-app-gitlab
  RewriteEngine on

  # Enable WebSocket reverse Proxy
  # Needs proxy_wstunnel enabled
  RewriteCond %{HTTP:Upgrade} websocket [NC]
  RewriteCond %{HTTP:Connection} upgrade [NC]
  RewriteRule ^/?(.*) "ws://127.0.0.1:8181/$1" [P,L]

  #Forward all requests to gitlab-workhorse except existing files like error documents
  RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f [OR]
  RewriteCond %{REQUEST_URI} ^/uploads/.*
  RewriteRule .* http://127.0.0.1:8181%{REQUEST_URI} [P,QSA,NE]

  RequestHeader set X_FORWARDED_PROTO 'https'
  RequestHeader set X-Forwarded-Ssl on

  # needed for downloading attachments
  DocumentRoot /home/git/gitlab/public

  #Set up apache error documents, if back end goes down (i.e. 503 error) then a maintenance/deploy page is thrown up.
  ErrorDocument 404 /404.html
  ErrorDocument 422 /422.html
  ErrorDocument 500 /500.html
  ErrorDocument 502 /502.html
  ErrorDocument 503 /503.html

  # Debian and CentOS distribution defaults provided below
  LogFormat "%{X-Forwarded-For}i %l %u %t \"%r\" %>s %b" common_forwarded

  #For CentOS distributions use
  ErrorLog /var/log/httpd/logs/YOUR_SERVER_FQDN_error.log
  CustomLog /var/log/httpd/logs/YOUR_SERVER_FQDN_forwarded.log common_forwarded
  CustomLog /var/log/httpd/logs/YOUR_SERVER_FQDN_access.log combined env=!dontlog
  CustomLog /var/log/httpd/logs/YOUR_SERVER_FQDN.log combined

  #For Debian distributions use
  #ErrorLog /var/log/apache2/YOUR_SERVER_FQDN_error.log
  #CustomLog /var/log/apache2/YOUR_SERVER_FQDN_forwarded.log common_forwarded
  #CustomLog /var/log/apache2/YOUR_SERVER_FQDN_access.log combined env=!dontlog
  #CustomLog /var/log/apache2/YOUR_SERVER_FQDN.log combined

</VirtualHost>

