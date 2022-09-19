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
