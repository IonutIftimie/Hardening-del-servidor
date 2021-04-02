# we will inherit from  the Debian image on DockerHub
FROM debian

# set timezone so files' timestamps are correct
ENV TZ=Europe/Madrid

# install apache and php 7.3
# we include procps and telnet so you can use these with shell.sh prompt
RUN apt-get update -qq >/dev/null && apt-get install -y -qq procps telnet apache2 php7.3 -qq >/dev/null

# HTML server directory
WORKDIR /var/www/html
COPY . /var/www/html/


# The PHP app is going to save its state in /data so we make a /data inside the container
RUN mkdir /data && chown -R www-data /data && chmod 755 /data & chmod 777 -R /var/www/html/
#RUN echo "ServerTokens ProductOnly" >> /etc/apache2/apache2.conf
#RUN echo "ServerSignature Off" >> /etc/apache2/apache2.conf
RUN a2enmod ssl
RUN service apache2 restart
RUN mkdir /etc/apache2/ssl
COPY apache.key /etc/apache2/ssl/
COPY apache.crt /etc/apache2/ssl/
COPY default-ssl.conf /etc/apache2/sites-available/
COPY hosts /etc/
RUN a2ensite default-ssl.conf
RUN service apache2 restart
RUN apt install vim -y
RUN rm /etc/apache2/apache2.conf
RUN rm /etc/apache2/conf-enabled/security.conf
RUN a2enmod headers
RUN apt install libapache2-mod-security2 -y
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
COPY 000-default.conf /etc/apache2/sites-enabled/
COPY security.conf /etc/apache2/conf-enabled/
COPY apache2.conf /etc/apache2/
RUN a2dismod --force autoindex
# we need custom php configuration file to enable userdirs
#COPY php.conf /etc/apache2/mods-available/php7.3.conf

# we run a script to stat the server; the array syntax makes it so ^C will work as we want
CMD  ["./entrypoint.sh"]
