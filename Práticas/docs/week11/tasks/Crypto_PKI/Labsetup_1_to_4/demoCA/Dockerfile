FROM handsonsecurity/seed-server:apache-php

ARG WWWDIR=/var/www/diogonunes2022

COPY ./index.html ./index_red.html $WWWDIR/
COPY ./diogonunes2022_apache_ssl.conf /etc/apache2/sites-available
COPY ./certs/server.crt ./certs/server.key  /certs/

RUN  chmod 400 /certs/server.key \
     && chmod 644 $WWWDIR/index.html \
     && chmod 644 $WWWDIR/index_red.html \
     && a2ensite diogonunes2022_apache_ssl   

CMD  tail -f /dev/null

