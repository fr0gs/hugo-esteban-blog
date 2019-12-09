+++
author = "Esteban"
categories = ["docker", "dockerfile"]
date = 0001-01-01T00:00:00Z
description = "When building docker images it is possible to provide variable arguments to provide an additional layer of customization. This post gives a brief overview."
draft = false
image = "/images/2017/07/docker-logo.png"
slug = "passing-arguments-to-dockerfiles"
tags = ["docker", "dockerfile"]
title = "Passing arguments to Dockerfiles"

+++


# Introduction

When using [docker](https://www.docker.com/) as our virtualization software of choice to deploy our applications, sometimes we might want to build an image that depends on a variable parameter, for example when building images from a script and you have a changing deployment folder when constructing it.

# Using the ENV keyword

The easiest way is to specify an environment variable inside the *Dockerfile* with the **ENV** keyword and then reference it from within the file. For instance, when you just need to update the version of a package and do some operations depending on that version:

```
FROM image
ENV PKG_VERSION 1.0.0
RUN curl http://my.cdn.com/package-$PKG_VERSION.zip
```

This **PKG_VERSION** was used as a build time variable but it is important to know that containers will be able to access it in runtime, which may lead to problems.

# Using the ARG keyword

The **ARG** keyword defines a variable that users can access at build time when constructing the image using the `--build-arg <variable>=<value>` option and then referencing it inside the *Dockerfile*. In the previous example the same result could be achieved by executing:

```sh
λ docker build -t my-image-name --build-arg PKG_VERSION=1.0.0 $PWD
```

*Dockerfile*:
```
FROM image
ARG PKG_VERSION
RUN curl http://my.cdn.com/package-$PKG_VERSION.zip
```

And in this case, the **PKG_VERSION** variable would only live during the build process, being unreachable from within the containers. Additionally, it is also possible to set **ARG** with a default value in case no `--build--arg` is specified:

*Dockerfile*:
```
FROM image
ARG PKG_VERSION=1.0.0
RUN curl http://my.cdn.com/package-$PKG_VERSION.zip
```

Of course, you could also define an environment variable that would depend on a value passed as an argument:

```sh
λ docker build -t my-image-name --build-arg PKG_VERSION=1.0.0 $PWD
```

*Dockerfile*:
```
FROM image
ARG VERSION_ARG=1.0.0
ENV PKG_VERSION=$VERSION_ARG
RUN curl http://my.cdn.com/package-$PKG_VERSION.zip
```

Now the passed argument **VERSION_ARG** will be available as the **PKG_VERSION** environment variable from within the container.

Moreover, if container's environment variables are preferred to be declared in runtime, it can be done easily when running it:

```sh
λ docker run -e ENV=development -e TIMEOUT=300 -e EXPORT_PATH=/exports ruby
```

<br>

Have fun!

