# Derived PHP images from original repo
Dockerized version of PHP, based on original PHP images, but with some additional module

## There are various versions depending on needs
- 7.4-apache : many modules (phpredis, mailparse, mysqli, bz2, gd, zip, pdo_mysql)
- 7.2-epe : very custom image
- ~~7.4-fpm : phpredis, pdo_mysql ~~ DON'T USE IT ANYMORE, use 7.4-fpm instead
- 7.4-fpm-redis : (phpredis, pdo_mysql)
- ~~7.4-apache-redis : (phpredis, pdo_mysql) and apache ~~ DON'T USE IT ANYMORE, use 7.4-apache instead
- 7.3-fpm : like 7.2-epe but with PHP 7.3
- 5.6-apache: many modules (mysqli, bz2, gd, zip, pdo_mysql). NO Redis support, NO mailparse support

## Environment Variables (for Apache 8.1 & 8.2)
| Name                             | Description                                                         | Default |
| -------------------------------- | ------------------------------------------------------------------- | ------- |
| APACHE_START_SERVERS             | Number of child server processes created at startup                 | 5       |
| APACHE_MIN_SPARE_SERVERS         | Minimum number of idle child server processes                       | 5       |
| APACHE_MAX_SPARE_SERVERS         | Maximum number of idle child server processes                       | 10      |
| APACHE_MAX_REQUEST_WORKERS       | Maximum number of connections that will be processed simultaneously | 150     |
| APACHE_MAX_CONNECTIONS_PER_CHILD | Max connections a child server will handle during its life          | 0       |
| APACHE_SERVER_LIMIT              | Upper limit on configurable number of processes                     | 256     |

