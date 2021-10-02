# Bitnami Docker Image for DreamFactory

## What is DreamFactory?

> DreamFactory is an open source REST API for mobile enterprise application developers. Add a REST API to any backend system. Services include SQL, NoSQL, BLOB, email, users, roles, security, and integration. Whether you're building a native or web-based app, DreamFactory developers can focus on creating great front-ends to their apps, while leaving all the backend work to DreamFactory.

https://www.dreamfactory.com/

## TL;DR

```console
$ curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-dreamfactory/master/docker-compose.yml > docker-compose.yml
$ docker-compose up -d
```

**Warning:** This quick setup is only intended for development environments. You are encouraged to change the insecure default credentials and check out the available configuration options in the [Environment Variables](#environment-variables) section for a more secure deployment.

## Why use Bitnami Images?

- Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
- With Bitnami images the latest bug fixes and features are available as soon as possible.
- Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
- All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading Linux distribution.
- All Bitnami images available in Docker Hub are signed with [Docker Content Trust (DCT)](https://docs.docker.com/engine/security/trust/content_trust/). You can use `DOCKER_CONTENT_TRUST=1` to verify the integrity of the images.
- Bitnami container images are released daily with the latest distribution packages available.

> This [CVE scan report](https://quay.io/repository/bitnami/dreamfactory?tab=tags) contains a security report with all open CVEs. To get the list of actionable security issues, find the "latest" tag, click the vulnerability report link under the corresponding "Security scan" field and then select the "Only show fixable" filter on the next page.

## Why use a non-root container?

Non-root container images add an extra layer of security and are generally recommended for production environments. However, because they run as a non-root user, privileged tasks are typically off-limits. Learn more about non-root containers [in our docs](https://docs.bitnami.com/tutorials/work-with-non-root-containers/).

## Supported tags and respective `Dockerfile` links

Learn more about the Bitnami tagging policy and the difference between rolling tags and immutable tags [in our documentation page](https://docs.bitnami.com/tutorials/understand-rolling-tags-containers/).


- [`4`, `4-debian-10`, `4.8.0`, `4.8.0-debian-10-r90`, `latest` (4/debian-10/Dockerfile)](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/4.8.0-debian-10-r90/4/debian-10/Dockerfile)

Subscribe to project updates by watching the [bitnami/dreamfactory GitHub repo](https://github.com/bitnami/bitnami-docker-dreamfactory).

## Get this image

The recommended way to get the Bitnami DreamFactory Docker Image is to pull the prebuilt image from the [Docker Hub Registry](https://hub.docker.com/r/bitnami/dreamfactory).

```console
$ docker pull bitnami/dreamfactory:latest
```

To use a specific version, you can pull a versioned tag. You can view the [list of available versions](https://hub.docker.com/r/bitnami/dreamfactory/tags/) in the Docker Hub Registry.

```console
$ docker pull bitnami/dreamfactory:[TAG]
```

If you wish, you can also build the image yourself.

```console
$ docker build -t bitnami/dreamfactory:latest 'https://github.com/bitnami/bitnami-docker-dreamfactory.git#master:4/debian-10'
```

## How to use this image

DreamFactory requires access to a MySQL or MariaDB database to store information. We'll use the [Bitnami Docker Image for MariaDB](https://www.github.com/bitnami/bitnami-docker-mariadb) for the database requirements.

### Run the application using Docker Compose

The main folder of this repository contains a functional [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file. Run the application using it as shown below:

```console
$ curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-dreamfactory/master/docker-compose.yml > docker-compose.yml
$ docker-compose up -d
```

### Using the Docker Command Line

If you want to run the application manually instead of using `docker-compose`, these are the basic steps you need to run:

#### Step 1: Create a network

```console
$ docker network create dreamfactory-network
```

#### Step 2: Create a volume for MariaDB persistence and create a MariaDB container

```console
$ docker volume create --name mariadb_data
$ docker run -d --name mariadb \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_dreamfactory \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_dreamfactory \
  --network dreamfactory-network \
  --volume mariadb_data:/bitnami/mariadb \
  bitnami/mariadb:latest
```

#### Step 3: Create a volume for MongoDB&reg; persistence and create a MongoDB&reg; container

```console
$ docker volume create --name mongodb_data
$ docker run -d --name mongodb \
  --network dreamfactory-network \
  --volume mongodb_data:/bitnami/mongodb \
  bitnami/mongob:latest
```

#### Step 4: Create a volume for Redis(TM) persistence and create a Redis(TM) container

```console
$ docker volume create --name redis_data
$ docker run -d --name redis \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --net dreamfactory-network \
  --volume redis_data:/bitnami/redis \
  bitnami/redis:latest
```

#### Step 4: Create volumes for DreamFactory persistence and launch the container

```console
$ docker volume create --name dreamfactory_data
$ docker run -d --name dreamfactory \
  -p 8080:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env DREAMFACTORY_DATABASE_USER=bn_dreamfactory \
  --env DREAMFACTORY_DATABASE_PASSWORD=bitnami \
  --env DREAMFACTORY_DATABASE_NAME=bitnami_dreamfactory \
  --network dreamfactory-network \
  --volume dreamfactory_data:/bitnami/dreamfactory \
  bitnami/dreamfactory:latest
```

Access your application at *http://your-ip/*

## Persisting your application

If you remove the container all your data will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a directory at the `/bitnami/dreamfactory` path. If the mounted directory is empty, it will be initialized on the first run. Additionally you should [mount a volume for persistence of the MariaDB data](https://github.com/bitnami/bitnami-docker-mariadb#persisting-your-database).

The above examples define the Docker volumes named `mariadb_data`, `mongodb_data`, `redis_data` and `dreamfactory_data`. The DreamFactory application state will persist as long as volumes are not removed.

To avoid inadvertent removal of volumes, you can [mount host directories as data volumes](https://docs.docker.com/engine/tutorials/dockervolumes/). Alternatively you can make use of volume plugins to host the volume data.

### Mount host directories as data volumes with Docker Compose

This requires a minor change to the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository:

```diff
   mariadb:
     ...
     volumes:
-      - 'mariadb_data:/bitnami/mariadb'
+      - /path/to/mariadb-persistence:/bitnami/mariadb
   ...
   mongodb:
     ...
     volumes:
-      - 'mongodb_data:/bitnami/mongodb'
+      - /path/to/mongodb-persistence:/bitnami/mongodb
   ...
   redis:
     ...
     volumes:
-      - 'redis_data:/bitnami/redis'
+      - /path/to/redis-persistence:/bitnami/redis
   ...
   dreamfactory:
     ...
     volumes:
-      - 'dreamfactory_data:/bitnami/dreamfactory'
+      - /path/to/dreamfactory-persistence:/bitnami/dreamfactory
   ...
-volumes:
-  mariadb_data:
-    driver: local
-  mongodb_data:
-    driver: local
-  redis_data:
-    driver: local
-  dreamfactory_data:
-    driver: local
```

> NOTE: As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

### Mount host directories as data volumes using the Docker command line

#### Step 1: Create a network (if it does not exist)

```console
$ docker network create dreamfactory-network
```

#### Step 2. Create a MariaDB container with host volume

```console
$ docker run -d --name mariadb \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env MARIADB_USER=bn_dreamfactory \
  --env MARIADB_PASSWORD=bitnami \
  --env MARIADB_DATABASE=bitnami_dreamfactory \
  --network dreamfactory-network \
  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
  bitnami/mariadb:latest
```

> NOTE: As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

#### Step 3. Create a MariaDB container with host volume

```console
$ docker run -d --name mongodb \
  --network dreamfactory-network \
  --volume /path/to/mongodb-persistence:/bitnami/mongodb \
  bitnami/mongodb:latest
```

> NOTE: As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

#### Step 4. Create a MariaDB container with host volume

```console
$ docker run -d --name redis \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --network dreamfactory-network \
  --volume /path/to/redis-persistence:/bitnami/redis \
  bitnami/redis:latest
```

> NOTE: As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

#### Step 5. Create the DreamFactory container with host volumes

```console
$ docker run -d --name dreamfactory \
  -p 8080:8080 -p 8443:8443 \
  --env ALLOW_EMPTY_PASSWORD=yes \
  --env DREAMFACTORY_DATABASE_USER=bn_dreamfactory \
  --env DREAMFACTORY_DATABASE_PASSWORD=bitnami \
  --env DREAMFACTORY_DATABASE_NAME=bitnami_dreamfactory \
  --network dreamfactory-network \
  --volume /path/to/dreamfactory-persistence:/bitnami/dreamfactory \
  bitnami/dreamfactory:latest
```

> NOTE: As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

## Configuration

### Environment variables

When you start the DreamFactory image, you can adjust the configuration of the instance by passing one or more environment variables either on the docker-compose file or on the `docker run` command line. If you want to add a new environment variable:

- For docker-compose add the variable name and value under the application section in the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository:

    ```yaml
    dreamfactory:
      ...
      environment:
        - DREAMFACTORY_PASSWORD=my_password
      ...
    ```

- For manual execution add a `--env` option with each variable and value:

    ```console
    $ docker run -d --name dreamfactory -p 80:8080 -p 443:8443 \
      --env DREAMFACTORY_PASSWORD=my_password \
      --network dreamfactory-tier \
      --volume /path/to/dreamfactory-persistence:/bitnami \
      bitnami/dreamfactory:latest
    ```

Available environment variables:

##### User and Site configuration

- `DREAMFACTORY_CREATE_ADMIN_ACCOUNT`: Whether to create a DreamFactory admin user account. Default: **no**
- `DREAMFACTORY_EMAIL`: DreamFactory application email. Will be ignored if `DREAMFACTORY_CREATE_ADMIN_ACCOUNT` is not set. Default: **user@example.com**
- `DREAMFACTORY_PASSWORD`: DreamFactory application password. Will be ignored if `DREAMFACTORY_CREATE_ADMIN_ACCOUNT` is not set. Default: **bitnami**
- `DREAMFACTORY_FIRST_NAME`: DreamFactory user first name. Will be ignored if `DREAMFACTORY_CREATE_ADMIN_ACCOUNT` is not set. Default: **UserName**
- `DREAMFACTORY_LAST_NAME`: DreamFactory user last name. Will be ignored if `DREAMFACTORY_CREATE_ADMIN_ACCOUNT` is not set. Default: **LastName**
- `DREAMFACTORY_PHONE`: DreamFactory user phone number. Will be ignored if `DREAMFACTORY_CREATE_ADMIN_ACCOUNT` is not set. Default: **+1**
- `DREAMFACTORY_SKIP_BOOTSTRAP`: Whether to skip performing the initial bootstrapping for the application. This is necessary in case you use a database that already has DreamFactory data. Default: **no**

##### Database connection configuration

- `DREAMFACTORY_DATABASE_TYPE`: Database type where DreamFactory configuration will be stored. A DreamFactory service will be automatically created for this database. Valid values: `mariadb`, `postgresql`. Default: **mariadb**
- `DREAMFACTORY_DATABASE_HOST`: Hostname for the MariaDB or MySQL server. Default: **mariadb**
- `DREAMFACTORY_DATABASE_PORT_NUMBER`: Port used by the MariaDB or MySQL server. Default: **3306**
- `DREAMFACTORY_DATABASE_NAME`: Database name that DreamFactory will use to connect with the database. Default: **bitnami_dreamfactory**
- `DREAMFACTORY_DATABASE_USER`: Database user that DreamFactory will use to connect with the database. Default: **bn_dreamfactory**
- `DREAMFACTORY_DATABASE_PASSWORD`: Database password that DreamFactory will use to connect with the database. No default.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### Redis(TM) connection configuration

- `DREAMFACTORY_ENABLE_REDIS`: Whether to enable a Redis(TM) cache server for DreamFactory. Default: **yes**
- `DREAMFACTORY_REDIS_HOST`: Hostname for the Redis(TM) cache server. Default: **redis**
- `DREAMFACTORY_REDIS_PORT_NUMBER`: Port used by Redis(TM) cache server. Default: **6379**
- `DREAMFACTORY_REDIS_PASSWORD`: Password for the Redis(TM) cache server user. No default.

##### Create an extra MariaDB service for DreamFactory

- `DREAMFACTORY_ENABLE_MARIADB_SERVICE`: Whether to enable a MariaDB service in DreamFactory. Default: **no**
- `DREAMFACTORY_MARIADB_SERVICE_HOST`: Hostname for the extra MariaDB server. Default: **mariadb**
- `DREAMFACTORY_MARIADB_SERVICE_PORT_NUMBER`: Port used by the extra MariaDB server. Default: **3306**
- `DREAMFACTORY_MARIADB_SERVICE_DATABASE_NAME`: Database name to use for the extra MariaDB server. Default: **df**
- `DREAMFACTORY_MARIADB_SERVICE_DATABASE_USER`: Username for the extra MariaDB server. No default.
- `DREAMFACTORY_MARIADB_SERVICE_DATABASE_PASSWORD`: Password for the extra MariaDB server user. No default.

##### Create an extra PostgreSQL service for DreamFactory

- `DREAMFACTORY_ENABLE_POSTGRESQL_SERVICE`: Whether to enable a PostgreSQL service in DreamFactory. Default: **no**
- `DREAMFACTORY_POSTGRESQL_SERVICE_HOST`: Hostname for the extra PostgreSQL server. Default: **postgresql**
- `DREAMFACTORY_POSTGRESQL_SERVICE_PORT_NUMBER`: Port used by the extra PostgreSQL server. Default: **5432**
- `DREAMFACTORY_POSTGRESQL_SERVICE_DATABASE_NAME`: Database name to use for the extra PostgreSQL server. Default: **df**
- `DREAMFACTORY_POSTGRESQL_SERVICE_DATABASE_USER`: Username for the extra PostgreSQL server. No default.
- `DREAMFACTORY_POSTGRESQL_SERVICE_DATABASE_PASSWORD`: Password for the extra PostgreSQL server user. No default.

##### Create an extra MongoDB&reg; service for DreamFactory

- `DREAMFACTORY_ENABLE_MONGODB_SERVICE`: Whether to enable a MongoDB&reg; service in DreamFactory. Default: **no**
- `DREAMFACTORY_POSTGRESQL_SERVICE_HOST`: Hostname for the extra MongoDB&reg; server. Default: **mongodb**
- `DREAMFACTORY_POSTGRESQL_SERVICE_PORT_NUMBER`: Port used by the extra MongoDB&reg; server. Default: **27017**
- `DREAMFACTORY_POSTGRESQL_SERVICE_DATABASE_NAME`: Database name to use for the extra MongoDB&reg; server. Default: **df**
- `DREAMFACTORY_POSTGRESQL_SERVICE_DATABASE_USER`: Username for the extra MongoDB&reg; server. No default.
- `DREAMFACTORY_POSTGRESQL_SERVICE_DATABASE_PASSWORD`: Password for the extra MongoDB&reg; server. No default.

##### Create a MariaDB or MySQL database for DreamFactory using mysql-client

- `MYSQL_CLIENT_DATABASE_HOST`: Hostname for the MariaDB or MySQL server. Default: **mariadb**
- `MYSQL_CLIENT_DATABASE_PORT_NUMBER`: Port used by the MariaDB or MySQL server. Default: **3306**
- `MYSQL_CLIENT_DATABASE_ROOT_USER`: Database admin user. Default: **root**
- `MYSQL_CLIENT_DATABASE_ROOT_PASSWORD`: Database password for the database admin user. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_NAME`: New database to be created by the mysql-client module. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_USER`: New database user to be created by the mysql-client module. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_PASSWORD`: Database password for the `MYSQL_CLIENT_CREATE_DATABASE_USER` user. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_CHARACTER_SET`: Character set to use for the new database. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_COLLATE`: Database collation to use for the new database. No default.
- `MYSQL_CLIENT_ENABLE_SSL`: Whether to enable SSL connections for the new database. Default: **no**
- `MYSQL_CLIENT_SSL_CA_FILE`: Path to the SSL CA file for the new database. No default.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### Create a PostgreSQL database for DreamFactory using postgresql-client

- `POSTGRESQL_CLIENT_DATABASE_HOST`: Hostname for the PostgreSQL server. Default: **postgresql**
- `POSTGRESQL_CLIENT_DATABASE_PORT_NUMBER`: Port used by the PostgreSQL server. Default: **5432**
- `POSTGRESQL_CLIENT_POSTGRES_USER`: Database admin user. Default: **root**
- `POSTGRESQL_CLIENT_POSTGRES_PASSWORD`: Database password for the database admin user. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_NAME`: New database to be created by the postgresql-client module. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_USERNAME`: New database user to be created by the postgresql-client module. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_PASSWORD`: Database password for the `POSTGRESQL_CLIENT_CREATE_DATABASE_USERNAME` user. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_EXTENSIONS`: PostgreSQL extensions to enable in the specified database during the first initialization. No defaults.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### Create a MongoDB database for DreamFactory using mongodb-client

- `MONGODB_CLIENT_DATABASE_HOST`: Hostname for the MongoDB server. Default: **mongodb**
- `MONGODB_CLIENT_DATABASE_PORT_NUMBER`: Port used by the MongoDB server. Default: **27017**
- `MONGODB_CLIENT_DATABASE_ROOT_USER`: Database admin user. Default: **root**
- `MONGODB_CLIENT_DATABASE_ROOT_PASSWORD`: Database password for the database admin user. No defaults.
- `MONGODB_CLIENT_CREATE_DATABASE_NAME`: New database to be created by the mongodb-client module. No defaults.
- `MONGODB_CLIENT_CREATE_DATABASE_USERNAME`: New database user to be created by the mongodb-client module. No defaults.
- `MONGODB_CLIENT_CREATE_DATABASE_PASSWORD`: Database password for the `MONGODB_CLIENT_CREATE_DATABASE_USERNAME` user. No defaults.
- `MONGODB_CLIENT_EXTRA_FLAGS`: Extra flags when using the mongodb-client during initialization. No defaults.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### SMTP Configuration

To configure DreamFactory to send email using SMTP you can set the following environment variables:

- `DREAMFACTORY_SMTP_HOST`: SMTP host.
- `DREAMFACTORY_SMTP_PORT_NUMBER`: SMTP port.
- `DREAMFACTORY_SMTP_USER`: SMTP account user.
- `DREAMFACTORY_SMTP_PASSWORD`: SMTP account password.
- `DREAMFACTORY_SMTP_PROTOCOL`: If specified, SMTP protocol to use. Allowed values: *tls*, *ssl*. No default.

##### PHP configuration

- `PHP_ENABLE_OPCACHE`: Enable OPcache for PHP scripts. Default: **yes**
- `PHP_EXPOSE_PHP`: Enables HTTP header with PHP version. No default.
- `PHP_MAX_EXECUTION_TIME`: Maximum execution time for PHP scripts. No default.
- `PHP_MAX_INPUT_TIME`: Maximum input time for PHP scripts. No default.
- `PHP_MAX_INPUT_VARS`: Maximum amount of input variables for PHP scripts. No default.
- `PHP_MEMORY_LIMIT`: Memory limit for PHP scripts. Default: **256M**
- `PHP_POST_MAX_SIZE`: Maximum size for PHP POST requests. No default.
- `PHP_UPLOAD_MAX_FILESIZE`: Maximum file size for PHP uploads. No default.

#### Examples

##### SMTP configuration using a Gmail account

This would be an example of SMTP configuration using a Gmail account:

- Modify the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository:

    ```yaml
      dreamfactory:
        ...
        environment:
          - DREAMFACTORY_DATABASE_USER=bn_dreamfactory
          - DREAMFACTORY_DATABASE_NAME=bitnami_dreamfactory
          - ALLOW_EMPTY_PASSWORD=yes
          - DREAMFACTORY_SMTP_HOST=smtp.gmail.com
          - DREAMFACTORY_SMTP_PORT_NUMBER=587
          - DREAMFACTORY_SMTP_USER=your_email@gmail.com
          - DREAMFACTORY_SMTP_PASSWORD=your_password
      ...
    ```

- For manual execution:

    ```console
    $ docker run -d --name dreamfactory -p 80:8080 -p 443:8443 \
      --env DREAMFACTORY_DATABASE_USER=bn_dreamfactory \
      --env DREAMFACTORY_DATABASE_NAME=bitnami_dreamfactory \
      --env DREAMFACTORY_SMTP_HOST=smtp.gmail.com \
      --env DREAMFACTORY_SMTP_PORT_NUMBER=587 \
      --env DREAMFACTORY_SMTP_USER=your_email@gmail.com \
      --env DREAMFACTORY_SMTP_PASSWORD=your_password \
      --network dreamfactory-tier \
      --volume /path/to/dreamfactory-persistence:/bitnami \
      bitnami/dreamfactory:latest
    ```

##### Connect DreamFactory container to an existing database

The Bitnami DreamFactory container supports connecting the DreamFactory application to an external database. This would be an example of using an external database for DreamFactory.

- Modify the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository:

    ```diff
       dreamfactory:
         ...
         environment:
    -      - DREAMFACTORY_DATABASE_HOST=mariadb
    +      - DREAMFACTORY_DATABASE_HOST=mariadb_host
           - DREAMFACTORY_DATABASE_PORT_NUMBER=3306
           - DREAMFACTORY_DATABASE_NAME=dreamfactory_db
           - DREAMFACTORY_DATABASE_USER=dreamfactory_user
    -      - ALLOW_EMPTY_PASSWORD=yes
    +      - DREAMFACTORY_DATABASE_PASSWORD=dreamfactory_password
         ...
    ```

- For manual execution:

    ```console
    $ docker run -d --name dreamfactory\
      -p 8080:8080 -p 8443:8443 \
      --network dreamfactory-network \
      --env DREAMFACTORY_DATABASE_HOST=mariadb_host \
      --env DREAMFACTORY_DATABASE_PORT_NUMBER=3306 \
      --env DREAMFACTORY_DATABASE_NAME=dreamfactory_db \
      --env DREAMFACTORY_DATABASE_USER=dreamfactory_user \
      --env DREAMFACTORY_DATABASE_PASSWORD=dreamfactory_password \
      --volume dreamfactory_data:/bitnami/dreamfactory \
      bitnami/dreamfactory:latest
    ```

In case the database already contains data from a previous DreamFactory installation, you need to set the variable `DREAMFACTORY_SKIP_BOOTSTRAP` to `yes`. Otherwise, the container would execute the installation wizard and could modify the existing data in the database. Note that, when setting `DREAMFACTORY_SKIP_BOOTSTRAP` to `yes`, values for environment variables such as `DREAMFACTORY_USERNAME`, `DREAMFACTORY_PASSWORD` or `DREAMFACTORY_EMAIL` will be ignored.

## Logging

The Bitnami DreamFactory Docker image sends the container logs to `stdout`. To view the logs:

```console
$ docker logs dreamfactory
```

Or using Docker Compose:

```console
$ docker-compose logs dreamfactory
```

You can configure the containers [logging driver](https://docs.docker.com/engine/admin/logging/overview/) using the `--log-driver` option if you wish to consume the container logs differently. In the default configuration docker uses the `json-file` driver.

## Maintenance

### Backing up your container

To backup your data, configuration and logs, follow these simple steps:

#### Step 1: Stop the currently running container

```console
$ docker stop dreamfactory
```

Or using Docker Compose:

```console
$ docker-compose stop dreamfactory
```

#### Step 2: Run the backup command

We need to mount two volumes in a container we will use to create the backup: a directory on your host to store the backup in, and the volumes from the container we just stopped so we can access the data.

```console
$ docker run --rm -v /path/to/dreamfactory-backups:/backups --volumes-from dreamfactory busybox \
  cp -a /bitnami/dreamfactory /backups/latest
```

### Restoring a backup

Restoring a backup is as simple as mounting the backup as volumes in the containers.

For the MariaDB database container:

```diff
 $ docker run -d --name mariadb \
   ...
-  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
+  --volume /path/to/mariadb-backups/latest:/bitnami/mariadb \
   bitnami/mariadb:latest
```

For the DreamFactory container:

```diff
 $ docker run -d --name dreamfactory \
   ...
-  --volume /path/to/dreamfactory-persistence:/bitnami/dreamfactory \
+  --volume /path/to/dreamfactory-backups/latest:/bitnami/dreamfactory \
   bitnami/dreamfactory:latest
```

### Upgrade this image

Bitnami provides up-to-date versions of MariaDB, MongoDB&reg;, PostgreSQL, Redis(TM) and DreamFactory, including security patches, soon after they are made upstream. We recommend that you follow these steps to upgrade your container. We will cover here the upgrade of the DreamFactory container. For the MariaDB upgrade see https://github.com/bitnami/bitnami-docker-mariadb/blob/master/README.md#upgrade-this-image. For the MongoDB® upgrade see https://github.com/bitnami/bitnami-docker-mongodb/blob/master/README.md#upgrade-this-image. For the Redis(TM) upgrade see https://github.com/bitnami/bitnami-docker-redis/blob/master/README.md#upgrade-this-image

The `bitnami/dreamfactory:latest` tag always points to the most recent release. To get the most recent release you can simple repull the `latest` tag from the Docker Hub with `docker pull bitnami/dreamfactory:latest`. However it is recommended to use [tagged versions](https://hub.docker.com/r/bitnami/dreamfactory/tags/).

#### Step 1: Get the updated image

```console
$ docker pull bitnami/dreamfactory:latest
```

#### Step 2: Stop the running container

Stop the currently running container using the command

```console
$ docker-compose stop dreamfactory
```

#### Step 3: Take a snapshot of the application state

Follow the steps in [Backing up your container](#backing-up-your-container) to take a snapshot of the current application state.

Additionally, snapshot the [MariaDB](https://github.com/bitnami/bitnami-docker-mariadb#step-2-stop-and-backup-the-currently-running-container), [MongoDB&reg;](https://github.com/bitnami/bitnami-docker-mongodb#step-2-stop-and-backup-the-currently-running-container) and [Redis(TM)](https://github.com/bitnami/bitnami-docker-redis#step-2-stop-and-backup-the-currently-running-container) data.

You can use these snapshots to restore the application state should the upgrade fail.

#### Step 4: Remove the currently running container

Remove the currently running container by executing the following command:

```console
docker-compose rm -v dreamfactory
```

#### Step 5: Run the new image

Update the image tag in `docker-compose.yml` and re-create your container with the new image:

```console
$ docker-compose up -d
```

## Customize this image

The Bitnami DreamFactory Docker image is designed to be extended so it can be used as the base image for your custom web applications.

### Extend this image

Before extending this image, please note there are certain configuration settings you can modify using the original image:

- Settings that can be adapted using environment variables. For instance, you can change the ports used by Apache for HTTP and HTTPS, by setting the environment variables `APACHE_HTTP_PORT_NUMBER` and `APACHE_HTTPS_PORT_NUMBER` respectively.
- [Adding custom virtual hosts](https://github.com/bitnami/bitnami-docker-apache#adding-custom-virtual-hosts).
- [Replacing the 'httpd.conf' file](https://github.com/bitnami/bitnami-docker-apache#full-configuration).
- [Using custom SSL certificates](https://github.com/bitnami/bitnami-docker-apache#using-custom-ssl-certificates).

If your desired customizations cannot be covered using the methods mentioned above, extend the image. To do so, create your own image using a Dockerfile with the format below:

```Dockerfile
FROM bitnami/dreamfactory
## Put your customizations below
...
```

Here is an example of extending the image with the following modifications:

- Install the `vim` editor
- Modify the Apache configuration file
- Modify the ports used by Apache

```Dockerfile
FROM bitnami/dreamfactory
LABEL maintainer "Bitnami <containers@bitnami.com>"

## Change user to perform privileged actions
USER 0
## Install 'vim'
RUN install_packages vim
## Revert to the original non-root user
USER 1001

## Enable mod_ratelimit module
RUN sed -i -r 's/#LoadModule ratelimit_module/LoadModule ratelimit_module/' /opt/bitnami/apache/conf/httpd.conf

## Modify the ports used by Apache by default
# It is also possible to change these environment variables at runtime
ENV APACHE_HTTP_PORT_NUMBER=8181
ENV APACHE_HTTPS_PORT_NUMBER=8143
EXPOSE 8181 8143
```

Based on the extended image, you can update the [`docker-compose.yml`](https://github.com/bitnami/bitnami-docker-dreamfactory/blob/master/docker-compose.yml) file present in this repository to add other features:

```diff
   dreamfactory:
-    image: bitnami/dreamfactory:latest
+    build: .
     ports:
-      - '80:8080'
-      - '443:8443'
+      - '80:8181'
+      - '443:8143'
     environment:
+      - PHP_MEMORY_LIMIT=512m
     ...
```

# Notable Changes

## 4.8.0-debian-10-r69

- The size of the container image has been decreased.
- The configuration logic is now based on Bash scripts in the *rootfs/* folder.
- The DreamFactory container has been migrated to a "non-root" user approach. Previously the container ran as the `root` user and the Apache daemon was started as the `daemon` user. From now on, both the container and the Apache daemon run as user `1001`. You can revert this behavior by changing `USER 1001` to `USER root` in the Dockerfile. Consequences:
  - The HTTP/HTTPS ports exposed by the container are now `8080/8443` instead of `80/443`.
  - Backwards compatibility is not guaranteed when data is persisted using docker or docker-compose. We highly recommend migrating the DreamFactory site by exporting its content, and importing it on a new DreamFactory container.

## 4.0.1-debian-10-r19

- PostgreSQL is now a supported database for storing DreamFactory configuration. Check the environment variable `DREAMFACTORY_DATABASE_TYPE` (default: `mariadb`). When `postgresql` is specified, a PostgreSQL database will be configured with the connection configuration in the environment variables `POSTGRESQL_HOST`, `POSTGRESQL_PORT_NUMBER`, `POSTGRESQL_USER` and `POSTGRESQL_PASSWORD`.
- It is possible to enable/disable the additional database service that is created by default, by setting the environment variable `DREAMFACTORY_ENABLE_MARIADB_DATABASE` for MariaDB database (default: `no`), `DREAMFACTORY_ENABLE_POSTGRESQL_DATABASE` for PostgreSQL database (default: `no`) and `DREAMFACTORY_ENABLE_MONGODB_DATABASE` for MongoDB&reg; database (default: `yes`). A service will be created for the default database type specified in `DREAMFACTORY_DATABASE_TYPE` (default: `mariadb`).

## 2.14.1-debian-9-r195 and 2.14.1-ol-7-r204

- This image has been adapted so it's easier to customize. See the [Customize this image](#customize-this-image) section for more information.
- The Apache configuration volume (`/bitnami/apache`) has been deprecated, and support for this feature will be dropped in the near future. Until then, the container will enable the Apache configuration from that volume if it exists. By default, and if the configuration volume does not exist, the configuration files will be regenerated each time the container is created. Users wanting to apply custom Apache configuration files are advised to mount a volume for the configuration at `/opt/bitnami/apache/conf`, or mount specific configuration files individually.
- The PHP configuration volume (`/bitnami/php`) has been deprecated, and support for this feature will be dropped in the near future. Until then, the container will enable the PHP configuration from that volume if it exists. By default, and if the configuration volume does not exist, the configuration files will be regenerated each time the container is created. Users wanting to apply custom PHP configuration files are advised to mount a volume for the configuration at `/opt/bitnami/php/conf`, or mount specific configuration files individually.
- Enabling custom Apache certificates by placing them at `/opt/bitnami/apache/certs` has been deprecated, and support for this functionality will be dropped in the near future. Users wanting to enable custom certificates are advised to mount their certificate files on top of the preconfigured ones at `/certs`.

## Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/bitnami-docker-dreamfactory/issues), or submit a [pull request](https://github.com/bitnami/bitnami-docker-dreamfactory/pulls) with your contribution.

## Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/bitnami-docker-dreamfactory/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

## License

Copyright (c) 2021 Bitnami

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
