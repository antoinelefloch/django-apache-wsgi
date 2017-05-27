# django-apache-wsgi
django-apache-wsgi

------------ create new user in docker container (/root not readable if using "root" user)\s\s
adduser bob

------------ config Apache HTTP server to use wsgi with Django source files in a docker container\s\s
Client connections that Apache receives will be translated into the WSGI format that the Django application expects using the mod_wsgi

https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-16-04

apt-get install libapache2-mod-wsgi-py3

django-admin startproject mygg

add this in   ~/mygg/mygg# vi settings.py \s\s
STATICFILES_DIRS = ( ) \s\s
STATIC_URL = '/static/' \s\s
STATIC_ROOT = os.path.join(BASE_DIR, 'static/') \s\s

./manage.py collectstatic \s\s
"78 static files copied to '~/mygg/static'."

vi /etc/apache2/sites-available/000-default.conf

        Alias /static /home/bob/mygg/static
        <Directory /home/bob/mygg/static>
          Require all granted
        </Directory>

        <Directory /home/bob/mygg/mygg>
          <Files wsgi.py>
            Require all granted
          </Files>
        </Directory>

        #<Directory /home/bob>
        #  <Files my-wsgi.py>
        #    Require all granted
        #  </Files>
        #</Directory>

        WSGIDaemonProcess mygg python-path=/home/bob/mygg
        WSGIProcessGroup mygg
        WSGIScriptAlias / /home/bob/mygg/mygg/wsgi.py
        #WSGIScriptAlias /myapp /home/bob/my-wsgi.py

/etc/init.d/apache2 restart \s\s

------------
useful: \s\s
apache2ctl configtest

