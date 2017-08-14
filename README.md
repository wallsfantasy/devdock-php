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
    You might want to add more hosts as you need, but using `http://dev.dock/your-project` allow you to easily reuse
    your host configuration in IDE.

3. Add more Nginx vhost configs using the example provided in `/docker/nginx/vhosts` These files need to have `.conf`
extension to be loaded by Nginx container.

4. Run `docker-compose up`. Every container log through `/dev/stdout` and `/dev/stderr` thus outputs are shown in
the terminal.

**Note**
We expect users to install package managements (composers, npm/yarn) from development machine as they are faster than
running them inside containers. Up to your framework, class cache might be generated using full directory path so
e.g. `artisan config:cache` need to run from within `cli` container to correctly generate path reference. 

## Containers
Almost every containers are official otherwise we use [Alpine](https://hub.docker.com/_/alpine/) as base image if it 
make our life easier (CLI and FPM in this case).

### CLI, FPM
CLI use to execute PHP console commands, run tests and take care of framework cache while FPM is designed solely to
process http requests. The reason is simply CLI often took longer/heavier tasks so they can't share the same php.ini.

#### Exposed Ports
- FPM: 9000 (internal)

#### Information
FPM status pages can be accessed at..
- <http://fpm.dock/status>
- <http://fpm.dock/ping>
- <http://fpm.dock/phpinfo.php>
- <http://fpm.dock/server.php>

CLI you can run following scripts
- `docker-compose exec cli php /var/www/info/phpinfo.php`
- `docker-compose exec cli php /var/www/info/server.php`

#### xdebug
Both (PHP) CLI and FPM are pre-installed with xdebug and has slightly different config. Because the request hits FPM 
from developer's machine it can "call back" the originator. OTOH, in CLI we `sh` and execute script inside the 
container thus we need to tell xdebug which host/ip is developer's machine. We can specify it in CLI's `xdebug.ini`.

To find host ip for CLI's xdebug config run this command inside CLI container
    
    /sbin/ip route|awk '/default/ { print $3 }'

### Nginx

#### Exposed Ports
- http: 80 (internal & host)
- https: 443 (internal & host)

#### Information
- <http://nginx.dock/status> 


### PostgreSql

#### Exposed Ports
- 5432 (internal & host)


### Selenium-Chrome
Currently we use standalone [selenium-chrome-debug](https://github.com/SeleniumHQ/docker-selenium) so we can observe the
(ghost) browser via VNC

#### VNC Configuration Gotchas
- Use `localhost:5900` as we exposed to host port.
- We use no username and selenium's default password which is secret ;)
- Set VNC color depth to 16 bit as low (e.g. 256) colors will immediately disconnected.

#### Exposed Ports
- Selenium: 4444 (internal)
- VNC: 5900 (internal & host)
