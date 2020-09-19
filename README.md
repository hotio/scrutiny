# scrutiny

<img src="https://raw.githubusercontent.com/hotio/docker-scrutiny/master/img/scrutiny.png" alt="Logo" height="130">

![Base](https://img.shields.io/badge/base-alpine-blue)
[![GitHub](https://img.shields.io/badge/source-github-lightgrey)](https://github.com/hotio/docker-scrutiny)
[![Docker Pulls](https://img.shields.io/docker/pulls/hotio/scrutiny)](https://hub.docker.com/r/hotio/scrutiny)
[![GitHub Registry](https://img.shields.io/badge/registry-ghcr.io-blue)](https://github.com/users/hotio/packages/container/scrutiny/versions)
[![Discord](https://img.shields.io/discord/610068305893523457?color=738ad6&label=discord&logo=discord&logoColor=white)](https://discord.gg/3SnkuKp)
[![Upstream](https://img.shields.io/badge/upstream-project-yellow)](https://github.com/AnalogJ/scrutiny)

## Starting the container

Just the basics to get the container running:

```shell
docker run --rm --name scrutiny -p 8080:8080 \
    --privileged \
    -v /run/udev:/run/udev:ro \
    -v /dev/disk:/dev/disk:ro \
    -v /<host_folder_config>:/config
    hotio/scrutiny
```

The environment variables below are all optional, the values you see are the defaults.

```shell
-e PUID=1000
-e PGID=1000
-e UMASK=002
-e TZ="Etc/UTC"
-e ARGS=""
-e DEBUG="no"
-e INTERVAL=86400
-e API_ENDPOINT="http://localhost:8080"
-e MODE="both"
```

For the environment variable `MODE` you can pick the values `both`, `web` or `collector` to enable the desired operating mode (see below). The `INTERVAL` variable defines the amount of time in seconds between collector runs, the metrics are pushed to the webinterface located at `API_ENDPOINT`.

## Deploying as 2 seperate containers

```shell
docker run --rm --name scrutiny-collector \
    --privileged \
    --network my-net \
    -v /run/udev:/run/udev:ro \
    -v /dev/disk:/dev/disk:ro \
    -v /<host_folder_config>:/config \
    -e INTERVAL=3600 \
    -e API_ENDPOINT="http://scrutiny-web:8080" \
    -e MODE="collector" \
    hotio/scrutiny
```

```shell
docker run --rm --name scrutiny-web \
    --network my-net \
    -p 8080:8080 \
    -v /<host_folder_config>:/config \
    -e MODE="web" \
    hotio/scrutiny
```

## Tags

| Tag       | Description                                |
| ----------|--------------------------------------------|
| latest    | The same as `stable`                       |
| stable    | Stable version                             |
| unstable  | Every commit to master branch              |

You can also find tags that reference a commit or version number.

## Configuration location

Your scrutiny configuration inside the container is stored in `/config/app`, to migrate from another container, you'd probably have to move your files from `/config` to `/config/app`.

## Executing your own scripts

If you have a need to do additional stuff when the container starts or stops, you can mount your script with `-v /docker/host/my-script.sh:/etc/cont-init.d/99-my-script` to execute your script on container start or `-v /docker/host/my-script.sh:/etc/cont-finish.d/99-my-script` to execute it when the container stops. An example script can be seen below.

```shell
#!/usr/bin/with-contenv bash

echo "Hello, this is me, your script."
```

## Troubleshooting a problem

By default all output is redirected to `/dev/null`, so you won't see anything from the application when using `docker logs`. Most applications write everything to a log file too. If you do want to see this output with `docker logs`, you can use `-e DEBUG="yes"` to enable this.
