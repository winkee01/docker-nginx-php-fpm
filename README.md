## Docker Nginx PHP-FPM Mariadb

This project use ``docker-compose`` to deploy a wordpress website on local machine.

You can run this site on current directory by simply execute `docker-compose
up```````````````````,

``Some key points

- The site is served on https and the default domain is ``testwp.ok``.
- To be able to use https, you should import the default self-signed certificate
    into your browser or keychain (on macOS).
- You can generate your own self-signed certificate and specify your preferred
    domain name. To make life easier, you can just use the default certificate and
    domain.

Below is an example to make a self-signed certificate:
```
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -nodes \
  -keyout testwp.key -out testwp.crt -subj '/CN=testwp.ok' \
  -addext 'subjectAltName=DNS:testwp.ok,IP:127.0.0.1'
```

- PHP-FPM in container will listen on unix sock, you can change it(in
    `php-fpm.conf`) to tcp port if you like.

- for nginx in a container to have the permissions to access the unix socket
    that php-fpm spawns in a different container, they must have the same user
    and group, as well as their uid and gid. Otherwise, you will encounter 502
    error and the reason in nginx's error.log shows is permission denied.

for example, by default, php-fpm container has built in user ``www-data`` and
php-fpm process will be started using this user. On the other hand, nginx
container has built in user `www-data` and `nginx`, but will start nginx process
using user `nginx` by default, we need to change it to `www-data`. Also, we
need to change its uid and gid so that their match to the corresponding values
in php-fpm container.

Below is the command to achieve this (after you execute `docker-compose up`):

```
docker exec -it nginx-php bash
usermod -u 82 www-data && groupmod -g 82 www-data && nginx -s reload
```
After you follow all the above steps, you can type the url `testwp.ok` in your browser
and access to the wordpress site. 


Enjoy!
