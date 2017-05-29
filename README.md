# django-apache-wsgi
django-apache-wsgi

tested with Apache 2.4.18
python 3.5.2

------------ create new user in docker container (/root not readable if using "root" user)  
adduser bob

------------ config Apache HTTP server to use wsgi with Django source files in a docker container  
Client connections that Apache receives will be translated into the WSGI format that the Django application expects using the mod_wsgi

https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-apache-and-mod_wsgi-on-ubuntu-16-04

apt-get install libapache2-mod-wsgi-py3

django-admin startproject mygg

add this in   ~/mygg/mygg# vi settings.py  
STATICFILES_DIRS = ( )  
STATIC_URL = '/static/'  
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')  

./manage.py collectstatic  
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

/etc/init.d/apache2 restart  

------------
useful:  
apache2ctl configtest

