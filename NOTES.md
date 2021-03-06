## TODO

ExecStart=/opt/bin/docker-compose --x-networking --x-network-driver host --file $REPO_DIR/docker-compose.yml up --force-recreate

  $ docker-compose --x-networking --x-network-driver host --file $REPO_DIR/docker-compose.yml build\n\

## Docker-compose 1.6.0 changes

https://github.com/docker/compose/blob/1.6.0-rc1/docs/compose-file.md

https://github.com/docker/compose/blob/1.6.0-rc1/docs/networking.md

## wp-cron.php

https://www.lucasrolff.com/wordpress/why-wp-cron-sucks/

WP specific settings in Nginx .conf files and SFTP access.

```
#*/5 * * * * php /var/www/pussybnb.com/wp-cron.php > /dev/null 2>&1
#*/5 * * * * php /var/www/pussybnb.com/wp-cron.php?doing_wp_cron > /dev/null 2>&1
#*/5 * * * * curl -vs -o /dev/null file:///var/www/pussybnb.com/wp-cron.php > /dev/null 2>&1

*/5 * * * * php /var/www/pussybnb.com/wp-cron.php
```

## Personal notes, only for development

Nginx was rebuilt from [source](https://www.nginx.com/resources/wiki/start/topics/tutorials/installoptions/) soon (list all the configured modules: `$ sudo nginx -V`).

Docker ENTRYPOINT vs CMD explanation: http://stackoverflow.com/a/21564990/1442219

PHP7 zend vs Source differences:

    Not installed in Zend PHP build: mysqlnd readline

    Not installed in source PHP build: pdo_mysql

    Zend -> Source

    OpenSSL Header Version:  OpenSSL 1.0.1f 6 Jan 2014  -> OpenSSL 1.0.1k 8 Jan 2015 

    PDO drivers:   mysql, sqlite -> sqlite

[Wordpress SSH2 tutorial](https://www.digitalocean.com/community/tutorials/how-to-configure-secure-updates-and-installations-in-wordpress-on-ubuntu)

cAdvisor + InfluxDB + Grafana monitoring stack compose script:

https://github.com/vegasbrianc/docker-monitoring

Nginx microcaching:

https://github.com/kevinohashi/WordPressVPS/blob/master/setup-nginx-php-fpm-microcache.sh

https://easyengine.io/wordpress-nginx/tutorials/single-site/fastcgi-cache-with-purging/

http://reviewsignal.com/blog/2014/06/25/40-million-hits-a-day-on-wordpress-using-a-10-vps/

Fine tuning here: /etc/defaults, /etc/sysctl.conf and /etc/security/limits.conf

Increase open files limit: https://easyengine.io/tutorials/linux/increase-open-files-limit/

EasyEngine tutorials: https://easyengine.io/tutorials/




Wordpress related:

https://deliciousbrains.com/http2-https-lets-encrypt-wordpress/

https://www.nginx.com/resources/wiki/start/topics/recipes/wordpress/

https://github.com/EasyEngine/easyengine

https://ttmm.io/tech/ludicrous-speed-wordpress-caching-with-redis/

https://www.digitalocean.com/community/tutorials/how-to-configure-redis-caching-to-speed-up-wordpress-on-ubuntu-14-04
http://www.jeedo.net/lightning-fast-wordpress-with-nginx-redis/

https://deliciousbrains.com/hosting-wordpress-yourself-server-monitoring-caching/

http://serverfault.com/questions/584403/nginx-cache-shared-between-multiple-servers

http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#example

Remove all docker containers:

    $ docker rm -f $(docker ps -a -q); docker rmi -f $(docker images -q); sudo rm -rf /var/lib/docker/volumes/*

    $ docker stop $(docker ps -a -q) && docker rm -f $(docker ps -a -q)
    $ docker rmi -f $(docker images -q)

Remove all unused images:

    $ docker images -q |xargs docker rmi

Remove all unused containers:

    $ docker ps -q |xargs docker rm

Make sure that exited containers are deleted:

    $ docker rm -v $(docker ps -a -q -f status=exited)

Remove unwanted ‘dangling’ images:

    $ docker rmi $(docker images -f "dangling=true" -q)

vfs dir final cleaning with a docker image:

    $ docker run -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker:/var/lib/docker --rm martin/docker-cleanup-volumes

Together:

    $ docker rm -v $(docker ps -a -q -f status=exited); docker rmi $(docker images -f "dangling=true" -q); docker run -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker:/var/lib/docker --rm martin/docker-cleanup-volumes
    $ rm -rf /var/lib/docker/overlay/*
    $ rm -rf /var/lib/docker/linkgraph.db

### Not installed for production, only in development

    $ curl vim man

    $ build-essential fakeroot devscripts module-assistant
    $ m-a prepare -y; m-a update

    $ python make gcc g++
    $ software-properties-common

Superuser tester script:

```
# Place su tester script in $HOME
RUN echo -e '\
#!/bin/bash\n\
echo "uid is ${UID}"\n\
echo "user is ${USER}"\n\
echo "username is ${USERNAME}"' >> /home/root/sutest.sh
```

```
#RUN cd /etc/ssh && stat -c "%a %n" *
#RUN cat /etc/group
#RUN cat /etc/passwd
```

```
#RUN sysctl -p
# Verifying new limits: max limit of file descriptors && Hard Limit && Soft Limit && Check limit for other user && Check limits of a running process
#RUN cat /proc/sys/fs/file-max \
#    && ulimit -Hn \
#    && ulimit -Sn \
#    && su - www-data -c 'ulimit -aHS' -s '/bin/bash' \
#    && ps aux | grep sshd
```

php install configs not included:

    RUN apt-get -y install \
        #libreadline6-dev --no-install-recommends \
        #libsqlite3-dev --no-install-recommends \
        # Own dependencies \
        #libpspell-dev --no-install-recommends \
        # Own dependencies - WP SSH2 \
        libssh2-1 \
        libssh2-1-dev --no-install-recommends



        # Configuring the build
        # http://wordpress.stackexchange.com/questions/42098/what-are-php-extensions-and-libraries-wp-needs-and-or-uses
        # https://wordpress.org/support/topic/wowdpress-php-dependencies
        # SAPI modules \
        #--disable-cli \
        # Extensions \
        #--enable-ftp \
        #--enable-pcntl \
        #--with-pdo-mysql=/usr \
        #--with-pspell \
        #--with-readline \
        #--enable-soap \
        #--enable-wddx \
        #--enable-mysqlnd \
        #--with-zlib \
        # Zend added \
        #--with-mysql=/usr \
        #--with-apxs2=/usr/bin/apxs2 \
        # Misc - Added by Docker installer \
        #--enable-mysqlnd \
        #--with-mysqli=/usr/bin/mysql_config \
        #--with-curl \
        #--with-openssl \
        #--with-readline \
        #--with-recode \
        #--with-zlib \

```
### Start of PECL SSH2 install
# https://github.com/php/pecl-networking-ssh2
RUN cd ~ \
    && mkdir -p /usr/src/ssh2 \
    && curl -fSL "https://github.com/php/pecl-networking-ssh2/archive/master.tar.gz" -o "pecl-networking-ssh2.tar.gz" \
    && tar -xzf "pecl-networking-ssh2.tar.gz" -C /usr/src/ssh2 --strip-components=1 \
    && rm -rf pecl-networking-ssh2.tar.gz \
    && cd /usr/src/ssh2 \
    && /usr/local/php7/bin/phpize \
    && ./configure \
        --with-ssh2 \
        --with-php-config=/usr/local/php7/bin/php-config \
    && make \
    && make install
### End of PECL SSH2 install
```

nginx install configs not included:

```
#RUN groupadd nginx -g 550 \
#    && useradd nginx -g nginx -u 550 --system --no-create-home -s /bin/false \
```

FFmpeg install configs removed:

```
RUN echo -e "\
deb http://httpredir.debian.org/debian jessie-backports main contrib non-free" > /etc/apt/sources.list.d/jessie-backports.list
RUN apt-get -y update && apt-get -y dist-upgrade \
    && apt-get -y -t jessie-backports install \
        libav-tools \
        ffmpeg
```

## Linux notes:

Available free space:

    $ df -h

## Crontab alternative:

```
#!/bin/bash
while true; do
  python /opt/com.org.project/main.py >> /opt/com.org.project/var/log/cron.log
  sleep 60
done

ENTRYPOINT ["/bin/bash", "/loop_main.sh" ]
```

## Running cron jobs with Docker:

http://stackoverflow.com/questions/34957338/if-adding-a-command-that-repeats-every-10-minutes-to-crontab-when-does-the-firs/34960575#34960575

https://www.ekito.fr/people/run-a-cron-job-with-docker/

## wp-cron alternatives

https://wordpress.org/plugins/wp-missed-schedule/faq/

https://wordpress.org/plugins/easycron/

https://yoast.com/wp-cron-control/

https://wordpress.org/plugins/wp-cron-control

https://wordpress.org/plugins/wp-crontrol/

## php cron removal

```
#/etc/init.d/rsyslog start

#Stay in foreground mode, don’t daemonize.
#/usr/sbin/cron -f &
#/usr/sbin/cron
#/etc/init.d/cron start
```

cron rsyslog

```
RUN apt-get -y install \
    vim

#cron fixes
#RUN touch /etc/crontab /etc/cron.d/* /var/spool/cron/crontabs/*
##COPY etc/cron.d /etc/cron.d
#COPY etc/crontab /etc/crontab
##COPY var/spool/cron/crontabs /var/spool/cron/crontabs
#RUN chmod 600 /etc/crontab /etc/cron.d/* /var/spool/cron/crontabs/*
#RUN touch /etc/crontab /etc/cron.d/* /var/spool/cron/crontabs/*

# Add crontab file in the cron directory
#ADD etc/cron.d/hello-cron /etc/cron.d/hello-cron
# Give execution rights on the cron job
#RUN chmod 0644 /etc/cron.d/hello-cron
# Create the log file to be able to run tail
#RUN touch /var/log/cron.log
```

```
# m h dom mon dow user  command
#*/5 *   * * *   www-data    php /var/www/pussybnb.com/wp-cron.php
#*/5 *   * * *   root    curl -vs -o /dev/null file:///var/www/pussybnb.com/wp-cron.php > /dev/null 2>&1
#*/5 *   * * *   www-data    /usr/local/php7/php /var/www/pussybnb.com/wp-cron.php > /dev/null 2>&1
#*/5 * * * * curl -vs -o /dev/null http://127.0.0.1/pussybnb.com/wp-cron.php > /dev/null 2>&1
#*/5 *   * * *   www-data    php /var/www/pussybnb.com/wp-cron.php > /dev/null 2>&1
#*/1 *   * * *   www-data    php /var/www/pussybnb.com/wp-cron.php >> /var/log/wp-cron.log 2>&1
#*/5 *   * * *   www-data    php /var/www/pussybnb.com/wp-cron.php >> /var/log/wp-cron.log 2>&1
*/1 *   * * *   root    date >> /var/log/cron-test.log 2>&1
```

```
    server {
    # server is one/domain!
        listen       80;
        listen       [::]:80 ipv6only=on;
        server_name  0.0.0.0;
        root   /var/www/domain;
        index  index.php index.html index.htm;
        set $skip_cache 1;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }    

        error_page  404              /404.html;
        location = /404.html {
            root   /etc/nginx/html;
            allow  all;
        }

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /etc/nginx/html;
        }

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            fastcgi_pass    unix:/var/run/php-fpm/php-fpm.sock;
            fastcgi_index   index.php;
            fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;

            try_files      $uri =404;

            fastcgi_connect_timeout 75;
            fastcgi_send_timeout 180;
            fastcgi_read_timeout 240;
            fastcgi_split_path_info ^(.+\.php)(/.+)$;

            fastcgi_intercept_errors on; 
        }
    }
```

```
        # Exclude folder from the domain
        location ^~ /domain {
            deny all;
        }
```
