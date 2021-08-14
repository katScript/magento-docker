# docker-magento

## Prerequisites

This setup assumes you are running Docker on a computer with at least 4GB of allocated RAM, a dual-core, and an SSD hard drive.

This configuration has been tested on Linux. Windows is supported through the use of Docker on WSL.

## Basic Setup

### Setup new magento projects
```bash
#bin/setup type version base_url
bin/setup ee 2.3.5-p1 magento2.test
```

The default admin user/password are admin/admin123. If you want to change to something else, go and check bin/setup file. It also contains other default configurations for other services such as redis, elasticsearch & rabbitMQ. The Magento source code is located at src folder.
After the installation finish, you can install sample data if you want

```bash
bin/sampledata 
```
### Setup with existing projects

```bash
bin/setup-project
```
Then the command will prompt you to input some information as below:
 - Git Url: git url of your project. Ex: https://github.com/katScript/magento-docker.git or git@github.com:katScript/magento-docker.git.
 - Branch: git branch you want to use. Ex: master.
 - Database: The backup database file. You can download the database, create new mysql folder and put it there then specify the path as input. Ex: mysql/m2.sql.
 - ENV File: Since we will not run the setup from the beginning, we need to provide the env.php file. Prepare the file then specify the path as input. Ex: env/env.php
 - Base Url: The base url you want to use on your local. The command will help to input to hosts file and setup the ssl self-certificate. Ex: magento2.test 

 ## Useful commands

 There are lots of commands you can use in bin folder. But in this section I will list down those I think important and may use everyday
 - `bin/status`: check the status of all containers.
 - `bin/start`: start the containers.
 - `bin/stop`: stop the containers.
 - `bin/restart`: restart the containers.
 - `bin/removeall`: remove all docker related things: containers, networks, volumes, and images.
 - `bin/composer`: Run the composer binary. Ex. `bin/composer install`
 - `bin/grunt`: Run the grunt binary. Ex. `bin/grunt exec`
 - `bin/magento`: Run the Magento CLI. Ex: `bin/magento cache:flush`
 - `bin/setup-grunt`: Install and configure Grunt JavaScript task runner to compile .less files
 - `bin/xdebug`: enable/disable xdebug. For detail instruction, refer to documentation.
 - `bin/dev-urn-catalog-generate`: Generate URN's for PHPStorm and remap paths to local host. Restart PHPStorm after running this command.
 - `bin/redis`: access redis.
 - `bin/mysql`: access to mysql container.
 - `bin/mysql-export`: export database.
 - `bin/mysql-import`: import database.
 - `bin/bash`: Drop into the bash prompt of your phpfpm Docker container. The `phpfpm` container should be mainly used to access the filesystem within Docker.
 - `bin/cli`: Run any CLI command without going into the bash prompt. Ex. `bin/cli ls`
 - `bin/clinotty`: Run any CLI command with no TTY. Ex. `bin/clinotty chmod u+x bin/magento`
 - `bin/fixowns`: This will fix filesystem ownerships within the container.
 - `bin/fixperms`: This will fix filesystem permissions within the container.

### Composer Authentication

First setup Magento Marketplace authentication (details in the [DevDocs](http://devdocs.magento.com/guides/v2.0/install-gde/prereq/connect-auth.html)).

When you do the setup, system will ask you to input the key then it will automatically save to disk. Incase, you want to do it manually, copy `src/auth.json.sample` to `src/auth.json`. Then, update the username and password values with your Magento public and private keys, respectively. Finally, copy the file to the container by running `bin/copytocontainer auth.json`.

### Email / Mailhog

View emails sent locally through Mailhog by visiting [http://{yourdomain}:8025](http://{yourdomain}:8025)

### Redis

Redis is now the default cache and session storage engine, and is automatically configured & enabled when running `bin/setup` on new installs.

### Elastic Search

In magento 2.4, elastic search will be default search engine and mysql search engine will be removed. So I recommend any new projects to use elastic search engine by default. You can access elastic search index via: http://localhost:9200/.

### RabbitMQ
The RabbitMQ box is already shipped with management plugin. You can access via: http://localhost:15672 . Default user/pass: guest/guest.

### Linux

Running Docker on Linux should be pretty straight-forward. Note that you need to run some [post install commands](https://docs.docker.com/install/linux/linux-postinstall/) as well as [installing Docker Compose](https://docs.docker.com/compose/install/). These steps are taken care of automatically with Docker Desktop, but not on Linux.

You may have to increase a virtual memory map count on the host system. It is required by [Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html).

Add following line to `/etc/sysctl.conf`:

```
vm.max_map_count=262144
```

### Blackfire.io

These docker images have built-in support for Blackfire.io. To use it, first register your server ID and token with the Blackfire agent:

```
bin/root blackfire-agent --register --server-id={YOUR_SERVER_ID} --server-token={YOUR_SERVER_TOKEN}
```

Next, open up the `bin/start` helper script and uncomment the line:

```
#bin/root /etc/init.d/blackfire-agent start
```

Finally, restart the containers with `bin/restart`. After doing so, everything is now configured and you can use a browser extension to profile your Magento store with Blackfire.

## License

[MIT](https://opensource.org/licenses/MIT)