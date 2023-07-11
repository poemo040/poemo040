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
amd64 and x86_64 refer to the same 64-bit architecture. The names arm64 and aarch64 are also interchangeable and refer to the same architecture.
sudo apt-get remove yarn

npm install --global yarn
This configuration has been tested on GitLab 8.2
# Note this config assumes unicorn is listening on default port 8080 and
# gitlab-workhorse is listening on port 8181. To allow gitlab-workhorse to
# listen on port 8181, edit or create /etc/default/gitlab and change or add the following:
#
# gitlab_workhorse_options="-listenUmask 0 -listenNetwork tcp -listenAddr 127.0.0.1:8181 -authBackend http://127.0.0.1:8080"
#
#Module dependencies
# mod_rewrite
# mod_proxy
# mod_proxy_http
<VirtualHost *:80>
  ServerName YOUR_SERVER_FQDN
  ServerSignature Off

  ProxyPreserveHost On

  # Ensure that encoded slashes are not decoded but left in their encoded state.
  # http://doc.gitlab.com/ce/api/projects.html#get-single-project
  AllowEncodedSlashes NoDecode

  <Location />
    Order deny,allow
    Allow from all

    #Allow forwarding to gitlab-workhorse
    ProxyPassReverse http://127.0.0.1:8181
    ProxyPassReverse http://YOUR_SERVER_FQDN/
  </Location>

  # Apache equivalent of nginx try files
  # http://serverfault.com/questions/290784/what-is-apaches-equivalent-of-nginxs-try-files
  # http://stackoverflow.com/questions/10954516/apache2-proxypass-for-rails-app-gitlab
  RewriteEngine on

  #Forward all requests to gitlab-workhorse except existing files like error documents
  RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f [OR]
  RewriteCond %{REQUEST_URI} ^/uploads/.*
  RewriteRule .* http://127.0.0.1:8181%{REQUEST_URI} [P,QSA,NE]

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

