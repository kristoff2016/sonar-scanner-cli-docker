[![Build Status](https://travis-ci.org/SonarSource/sonar-scanner-cli-docker.svg?branch=master)](https://travis-ci.org/SonarSource/sonar-scanner-cli-docker)

# SonarScanner CLI

This is the Git repository that contains source for [SonarScanner CLI](https://github.com/SonarSource/sonar-scanner-cli) Docker images.
Images are available on [Docker Hub](https://hub.docker.com/r/sonarsource/sonar-scanner-cli).

NB: These Docker images are not compatible with C/C++/Objective-C projects.

## Beta

This image is currently in Beta testing and is made available to gather feedback.

## License

Copyright 2015-2019 SonarSource.

Licensed under the [GNU Lesser General Public License, Version 3.0](http://www.gnu.org/licenses/lgpl.txt)

# How to run the Docker image

## On Linux

You can then run the following command:

```
docker run -e SONAR_HOST_URL=http://foo.acme:9000 --user="$(id -u):$(id -g)" -it -v "/path/to/project:/usr/src" sonarsource/sonar-scanner-cli
```

To analysis the project in the current directory:

```
docker run -e SONAR_HOST_URL=http://foo.acme:9000 --user="$(id -u):$(id -g)" -it -v "$PWD:/usr/src" sonarsource/sonar-scanner-cli
```

### Write permissions

The scanner writes to the analysed project's directory, in directory `${SONAR_PROJECT_BASE_DIR}/.scannerwork`.

By default scanner writes with user id `1000` and group id `1000`. The `--user` option (see sample commands above) is used on Linux to have the scanner write with the same user and group as the caller to the `docker run` command.

## On Mac

The command is quite similar to the one for Linux except that you don't need to specify `--user` option.

To analyse the project located in `/path/to/project`, execute:

```
docker run -e SONAR_HOST_URL=http://foo.acme:9000 -it -v "/path/to/project:/usr/src" sonarsource/sonar-scanner-cli
```

To analyse the project in the current directory, execute:

```
docker run -e SONAR_HOST_URL=http://foo.acme:9000 -it -v "$(pwd):/usr/src" sonarsource/sonar-scanner-cli
```

## Supported environment variables

### SonarQube host and authentication

* `SONAR_HOST_URL`: URL to the SonarQube instance the scanner should connect to (default=`http://localhost:9000`)
* `SONAR_TOKEN`: the prefered way to authenticate to SonarQube is to use a token. Use this environment variable to pass it to the scanner
* `SONAR_LOGIN` and `SONAR_PASSWORD`: alternatively, you can provide the SonarQube username and password for the scanner to use using these two variables

### Project mounting point

By default, the scanner analyses the project in directory `/usr/src`.

If you can't mount the project into this directory (eg. in Gitlab CI), use `SONAR_PROJECT_BASE_DIR` to specify another location in the container.

```
-e SONAR_PROJECT_BASE_DIR=/build
```

### Scanner user home

The scanner downloads data from the SonarQube server it connects to. Retrieving this data can take time and certainly takes bandwidth. For efficiency, the scanner caches this data in the user home (directory named `.sonar`).

When running the scanner with this image, this `.sonar` directory is created in the project's directory (see `SONAR_PROJECT_BASE_DIR`). This implies caching is not happening accross analysis of multiple projects.

Caching is actually shared between projects when running the scanner natively as the `.sonar` is created in the home directory of the current user (eg. `/home/my_user/.sonar`).

You can reproduce this behavior, by adding the following to the `docker run` command:

```
-e SONAR_USER_HOME=/usr/.sonar -v "/home/my_user/.sonar:/usr/.sonar"
```

# Developer documentation

Developer documentation is available in [DEVELOPER.md](DEVELOPER.md).
