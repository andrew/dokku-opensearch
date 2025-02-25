# dokku opensearch [![Build Status](https://img.shields.io/github/actions/workflow/status/andrew/dokku-opensearch/ci.yml?branch=master&style=flat-square "Build Status")](https://github.com/andrew/dokku-opensearch/actions/workflows/ci.yml?query=branch%3Amaster) [![IRC Network](https://img.shields.io/badge/irc-libera-blue.svg?style=flat-square "IRC Libera")](https://webchat.libera.chat/?channels=dokku)

Official opensearch plugin for dokku. Currently defaults to installing [opensearch 1.3.20](https://hub.docker.com/r/opensearchproject/opensearch).

## Requirements

- dokku 0.19.x+
- docker 1.8.x

## Installation

```shell
# on 0.19.x+
sudo dokku plugin:install https://github.com/andrew/dokku-opensearch.git opensearch
```

## Commands

```
opensearch:app-links <app>                      # list all opensearch service links for a given app
opensearch:backup-set-public-key-encryption <service> <public-key-id> # set GPG Public Key encryption for all future backups of opensearch service
opensearch:backup-unset-public-key-encryption <service> # unset GPG Public Key encryption for future backups of the opensearch service
opensearch:create <service> [--create-flags...] # create a opensearch service
opensearch:destroy <service> [-f|--force]       # delete the opensearch service/data/container if there are no links left
opensearch:enter <service>                      # enter or run a command in a running opensearch service container
opensearch:exists <service>                     # check if the opensearch service exists
opensearch:expose <service> <ports...>          # expose a opensearch service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)
opensearch:info <service> [--single-info-flag]  # print the service information
opensearch:link <service> <app> [--link-flags...] # link the opensearch service to the app
opensearch:linked <service> <app>               # check if the opensearch service is linked to an app
opensearch:links <service>                      # list all apps linked to the opensearch service
opensearch:list                                 # list all opensearch services
opensearch:logs <service> [-t|--tail] <tail-num-optional> # print the most recent log(s) for this service
opensearch:pause <service>                      # pause a running opensearch service
opensearch:promote <service> <app>              # promote service <service> as OPENSEARCH_URL in <app>
opensearch:restart <service>                    # graceful shutdown and restart of the opensearch service container
opensearch:set <service> <key> <value>          # set or clear a property for a service
opensearch:start <service>                      # start a previously stopped opensearch service
opensearch:stop <service>                       # stop a running opensearch service
opensearch:unexpose <service>                   # unexpose a previously exposed opensearch service
opensearch:unlink <service> <app>               # unlink the opensearch service from the app
opensearch:upgrade <service> [--upgrade-flags...] # upgrade service <service> to the specified versions
```

## Usage

Help for any commands can be displayed by specifying the command as an argument to opensearch:help. Plugin help output in conjunction with any files in the `docs/` folder is used to generate the plugin documentation. Please consult the `opensearch:help` command for any undocumented commands.

### Basic Usage

### create a opensearch service

```shell
# usage
dokku opensearch:create <service> [--create-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-m|--memory MEMORY`: container memory limit in megabytes (default: unlimited)
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-p|--password PASSWORD`: override the user-level service password
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-r|--root-password PASSWORD`: override the root-level service password
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for opensearch docker container

Create a opensearch service named lollipop:

```shell
dokku opensearch:create lollipop
```

You can also specify the image and image version to use for the service. It *must* be compatible with the opensearch image.

```shell
export OPENSEARCH_IMAGE="opensearch"
export OPENSEARCH_IMAGE_VERSION="${PLUGIN_IMAGE_VERSION}"
dokku opensearch:create lollipop
```

You can also specify custom environment variables to start the opensearch service in semicolon-separated form.

```shell
export OPENSEARCH_CUSTOM_ENV="USER=alpha;HOST=beta"
dokku opensearch:create lollipop
```

### print the service information

```shell
# usage
dokku opensearch:info <service> [--single-info-flag]
```

flags:

- `--config-dir`: show the service configuration directory
- `--data-dir`: show the service data directory
- `--dsn`: show the service DSN
- `--exposed-ports`: show service exposed ports
- `--id`: show the service container id
- `--internal-ip`: show the service internal ip
- `--initial-network`: show the initial network being connected to
- `--links`: show the service app links
- `--post-create-network`: show the networks to attach to after service container creation
- `--post-start-network`: show the networks to attach to after service container start
- `--service-root`: show the service root directory
- `--status`: show the service running status
- `--version`: show the service image version

Get connection information as follows:

```shell
dokku opensearch:info lollipop
```

You can also retrieve a specific piece of service info via flags:

```shell
dokku opensearch:info lollipop --config-dir
dokku opensearch:info lollipop --data-dir
dokku opensearch:info lollipop --dsn
dokku opensearch:info lollipop --exposed-ports
dokku opensearch:info lollipop --id
dokku opensearch:info lollipop --internal-ip
dokku opensearch:info lollipop --initial-network
dokku opensearch:info lollipop --links
dokku opensearch:info lollipop --post-create-network
dokku opensearch:info lollipop --post-start-network
dokku opensearch:info lollipop --service-root
dokku opensearch:info lollipop --status
dokku opensearch:info lollipop --version
```

### list all opensearch services

```shell
# usage
dokku opensearch:list
```

List all services:

```shell
dokku opensearch:list
```

### print the most recent log(s) for this service

```shell
# usage
dokku opensearch:logs <service> [-t|--tail] <tail-num-optional>
```

flags:

- `-t|--tail [<tail-num>]`: do not stop when end of the logs are reached and wait for additional output

You can tail logs for a particular service:

```shell
dokku opensearch:logs lollipop
```

By default, logs will not be tailed, but you can do this with the --tail flag:

```shell
dokku opensearch:logs lollipop --tail
```

The default tail setting is to show all logs, but an initial count can also be specified:

```shell
dokku opensearch:logs lollipop --tail 5
```

### link the opensearch service to the app

```shell
# usage
dokku opensearch:link <service> <app> [--link-flags...]
```

flags:

- `-a|--alias "BLUE_DATABASE"`: an alternative alias to use for linking to an app via environment variable
- `-q|--querystring "pool=5"`: ampersand delimited querystring arguments to append to the service link
- `-n|--no-restart "false"`: whether or not to restart the app on link (default: true)

A opensearch service can be linked to a container. This will use native docker links via the docker-options plugin. Here we link it to our `playground` app.

> NOTE: this will restart your app

```shell
dokku opensearch:link lollipop playground
```

The following environment variables will be set automatically by docker (not on the app itself, so they won’t be listed when calling dokku config):

```
DOKKU_OPENSEARCH_LOLLIPOP_NAME=/lollipop/DATABASE
DOKKU_OPENSEARCH_LOLLIPOP_PORT=tcp://172.17.0.1:9200
DOKKU_OPENSEARCH_LOLLIPOP_PORT_9200_TCP=tcp://172.17.0.1:9200
DOKKU_OPENSEARCH_LOLLIPOP_PORT_9200_TCP_PROTO=tcp
DOKKU_OPENSEARCH_LOLLIPOP_PORT_9200_TCP_PORT=9200
DOKKU_OPENSEARCH_LOLLIPOP_PORT_9200_TCP_ADDR=172.17.0.1
```

The following will be set on the linked application by default:

```
OPENSEARCH_URL=http://dokku-opensearch-lollipop:9200
```

The host exposed here only works internally in docker containers. If you want your container to be reachable from outside, you should use the `expose` subcommand. Another service can be linked to your app:

```shell
dokku opensearch:link other_service playground
```

It is possible to change the protocol for `OPENSEARCH_URL` by setting the environment variable `OPENSEARCH_DATABASE_SCHEME` on the app. Doing so will after linking will cause the plugin to think the service is not linked, and we advise you to unlink before proceeding.

```shell
dokku config:set playground OPENSEARCH_DATABASE_SCHEME=http2
dokku opensearch:link lollipop playground
```

This will cause `OPENSEARCH_URL` to be set as:

```
http2://dokku-opensearch-lollipop:9200
```

### unlink the opensearch service from the app

```shell
# usage
dokku opensearch:unlink <service> <app>
```

flags:

- `-n|--no-restart "false"`: whether or not to restart the app on unlink (default: true)

You can unlink a opensearch service:

> NOTE: this will restart your app and unset related environment variables

```shell
dokku opensearch:unlink lollipop playground
```

### set or clear a property for a service

```shell
# usage
dokku opensearch:set <service> <key> <value>
```

Set the network to attach after the service container is started:

```shell
dokku opensearch:set lollipop post-create-network custom-network
```

Set multiple networks:

```shell
dokku opensearch:set lollipop post-create-network custom-network,other-network
```

Unset the post-create-network value:

```shell
dokku opensearch:set lollipop post-create-network
```

### Service Lifecycle

The lifecycle of each service can be managed through the following commands:

### enter or run a command in a running opensearch service container

```shell
# usage
dokku opensearch:enter <service>
```

A bash prompt can be opened against a running service. Filesystem changes will not be saved to disk.

> NOTE: disconnecting from ssh while running this command may leave zombie processes due to moby/moby#9098

```shell
dokku opensearch:enter lollipop
```

You may also run a command directly against the service. Filesystem changes will not be saved to disk.

```shell
dokku opensearch:enter lollipop touch /tmp/test
```

### expose a opensearch service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)

```shell
# usage
dokku opensearch:expose <service> <ports...>
```

Expose the service on the service's normal ports, allowing access to it from the public interface (`0.0.0.0`):

```shell
dokku opensearch:expose lollipop 9200 9300
```

Expose the service on the service's normal ports, with the first on a specified ip adddress (127.0.0.1):

```shell
dokku opensearch:expose lollipop 127.0.0.1:9200 9300
```

### unexpose a previously exposed opensearch service

```shell
# usage
dokku opensearch:unexpose <service>
```

Unexpose the service, removing access to it from the public interface (`0.0.0.0`):

```shell
dokku opensearch:unexpose lollipop
```

### promote service <service> as OPENSEARCH_URL in <app>

```shell
# usage
dokku opensearch:promote <service> <app>
```

If you have a opensearch service linked to an app and try to link another opensearch service another link environment variable will be generated automatically:

```
DOKKU_OPENSEARCH_BLUE_URL=http://other_service:ANOTHER_PASSWORD@dokku-opensearch-other-service:9200/other_service
```

You can promote the new service to be the primary one:

> NOTE: this will restart your app

```shell
dokku opensearch:promote other_service playground
```

This will replace `OPENSEARCH_URL` with the url from other_service and generate another environment variable to hold the previous value if necessary. You could end up with the following for example:

```
OPENSEARCH_URL=http://other_service:ANOTHER_PASSWORD@dokku-opensearch-other-service:9200/other_service
DOKKU_OPENSEARCH_BLUE_URL=http://other_service:ANOTHER_PASSWORD@dokku-opensearch-other-service:9200/other_service
DOKKU_OPENSEARCH_SILVER_URL=http://lollipop:SOME_PASSWORD@dokku-opensearch-lollipop:9200/lollipop
```

### start a previously stopped opensearch service

```shell
# usage
dokku opensearch:start <service>
```

Start the service:

```shell
dokku opensearch:start lollipop
```

### stop a running opensearch service

```shell
# usage
dokku opensearch:stop <service>
```

Stop the service and removes the running container:

```shell
dokku opensearch:stop lollipop
```

### pause a running opensearch service

```shell
# usage
dokku opensearch:pause <service>
```

Pause the running container for the service:

```shell
dokku opensearch:pause lollipop
```

### graceful shutdown and restart of the opensearch service container

```shell
# usage
dokku opensearch:restart <service>
```

Restart the service:

```shell
dokku opensearch:restart lollipop
```

### upgrade service <service> to the specified versions

```shell
# usage
dokku opensearch:upgrade <service> [--upgrade-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-R|--restart-apps "true"`: whether or not to force an app restart (default: false)
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for opensearch docker container

You can upgrade an existing service to a new image or image-version:

```shell
dokku opensearch:upgrade lollipop
```

### Service Automation

Service scripting can be executed using the following commands:

### list all opensearch service links for a given app

```shell
# usage
dokku opensearch:app-links <app>
```

List all opensearch services that are linked to the `playground` app.

```shell
dokku opensearch:app-links playground
```

### check if the opensearch service exists

```shell
# usage
dokku opensearch:exists <service>
```

Here we check if the lollipop opensearch service exists.

```shell
dokku opensearch:exists lollipop
```

### check if the opensearch service is linked to an app

```shell
# usage
dokku opensearch:linked <service> <app>
```

Here we check if the lollipop opensearch service is linked to the `playground` app.

```shell
dokku opensearch:linked lollipop playground
```

### list all apps linked to the opensearch service

```shell
# usage
dokku opensearch:links <service>
```

List all apps linked to the `lollipop` opensearch service.

```shell
dokku opensearch:links lollipop
```
### Backups

Datastore backups are supported via AWS S3 and S3 compatible services like [minio](https://github.com/minio/minio).

You may skip the `backup-auth` step if your dokku install is running within EC2 and has access to the bucket via an IAM profile. In that case, use the `--use-iam` option with the `backup` command.

Backups can be performed using the backup commands:

### set GPG Public Key encryption for all future backups of opensearch service

```shell
# usage
dokku opensearch:backup-set-public-key-encryption <service> <public-key-id>
```

Set the `GPG` Public Key for encrypting backups:

```shell
dokku opensearch:backup-set-public-key-encryption lollipop
```

### unset GPG Public Key encryption for future backups of the opensearch service

```shell
# usage
dokku opensearch:backup-unset-public-key-encryption <service>
```

Unset the `GPG` Public Key encryption for backups:

```shell
dokku opensearch:backup-unset-public-key-encryption lollipop
```

### Disabling `docker image pull` calls

If you wish to disable the `docker image pull` calls that the plugin triggers, you may set the `OPENSEARCH_DISABLE_PULL` environment variable to `true`. Once disabled, you will need to pull the service image you wish to deploy as shown in the `stderr` output.

Please ensure the proper images are in place when `docker image pull` is disabled.
