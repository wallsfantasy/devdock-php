# devdock-php
Docker-Compose for PHP application development

This project inspired by [laradock.io](http://laradock.io) but designed to be more lightweight and do not aim toward
deployment on staging or production. Rather than hiding the complications with arguments, we want configurations easy
to read/modify/understand and shamelessly leave commented-out configs if it reminds common use-cases.

## Installation
1. Clone devdock in the same level of your development projects directory
    ```
    projects
      - dev-dock
      - project_a
      - project_b
      ...
    ```

2. edit your hosts file `/etc/hosts` and point the following domains to `127.0.0.1`
    ```
    /etc/hosts
    
    127.0.0.1    dev.dock nginx.dock fpm.dock
    ```
    You might need to add more hosts if you already use them in your development setting.

3. Add more Nginx vhost configs using the example provided in `/docker/nginx/vhosts` These files need to have `.conf`
extension to be loaded by Nginx container.

4. Run `docker-compose up`. Every container log through `/dev/stdout` and `/dev/stderr` thus outputs are shown in
the terminal.

**Note**
We expect users to install package managements (composers, npm/yarn) from local machine as they are not use to provision
the framework cache. Also running them bare metal is obviously faster. 

## Containers
Almost every containers are official otherwise we use [Alpine](https://alpinelinux.org) as base image if it make our
life easier (PHP related images in this case).  

### CLI, FPM
CLI use to execute PHP console commands, run tests and take care of framework cache while FPM is designed solely to
process http requests. The reason is simply CLI often took longer/heavier tasks so they can't share the same php.ini. 

Both (PHP) CLI and FPM are pre-installed with xdebug and has slightly different config. Because the request hits FPM
from developer's machine it can "call back" the originator. OTOH, for CLI we `sh` and execute script from the container
itself thus we need to explicitly configure our IP in `xdebug.ini`.

To find host ip for CLI's xdebug config run this command inside CLI container
    ```
    /sbin/ip route|awk '/default/ { print $3 }'`
    ```

### Nginx
(tbd)

### PostgreSql
(tbd)
