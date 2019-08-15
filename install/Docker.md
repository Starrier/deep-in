# Ubuntu Docker

## Install by using `wget`

1. Verify that wget is installed.

```shell
`which wget`
```

and if the wget is not installed, updrade the package manager and install it.

```shell
`sudo apt-get update`
`sudo apt-get install wget`
```

2. Get the latest version of the Docker.

```shell
`wget -qO -https://get.docker.com/ | sh`
```

3. check the docker

```shell
`docker version`
```