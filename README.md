# Supported tags and respective `Dockerfile` links

-	[`4.4.0`, `4.4.0-ubuntu18.04`, `latest`](https://github.com/BallAerospace/cosmos-docker/blob/master/ubuntu18.04/Dockerfile)
-	[`4.4.0-centos7`](https://github.com/BallAerospace/cosmos-docker/blob/master/centos7/Dockerfile)

# Quick reference

-	**Where to file issues**:  
	[https://github.com/BallAerospace/COSMOS/issues](https://github.com/BallAerospace/COSMOS/issues)

-	**Where to get help**:  
	[COSMOS Documentation](https://cosmosrb.com/), [COSMOS Issues on Github](https://github.com/BallAerospace/COSMOS/issues)

-	**Maintained by**:  
	[The Ball Aerospace COSMOS Team](https://github.com/BallAerospace/COSMOS)

-	**Supported architectures**:
  amd64

# What is Ball Aerospace COSMOS?

COSMOS is a suite of applications that can be used to control a set of embedded systems. These systems can be anything from test equipment (power supplies, oscilloscopes, switched power strips, UPS devices, etc), to development boards (Arduinos, Raspberry Pi, Beaglebone, etc), to satellites.

![logo](https://github.com/BallAerospace/COSMOS/blob/master/data/cosmos_word.gif?raw=true)

# How to use this image

## General Details

```console
# Linux
docker run --net=host --rm -e DISPLAY -e QT_X11_NO_MITSHM=1 ballaerospace/cosmos

# Windows
set DISPLAY=<My XServer's IP Address ie 10.0.0.1:0.0>
winpty docker run --net=host --rm -e DISPLAY -e QT_X11_NO_MITSHM=1 ballaerospace/cosmos
```

Requirements: A working Docker installation and an XServer.  For Windows, we recommend running docker through winpty.  It seems to fix the tty output and prevent container lockups especially when editing files in vi and accidently pressing multiple keys simulataneously on the keyboard.

COSMOS is a GUI based application, so running it from a Docker container requires your host to be running an XServer.  On a linux desktop, you probably have this already. On Windows there are several XServers available like Cygwin, XMing, and MobaXTerm. We recommend MobaXTerm because it seems to work well and is a simple standalone executable.

To run with MobaXTerm:

  1. Launcher MobaXTerm
  2. Select Settings -> Configuration, then X11, then set X11 remote access to: full.  Click Ok.
  3. Click the large Session button, and then click Shell, and click Ok.
  4. Note the Line: Your DISPLAY is set to X.X.X.X:X:0.   Make sure you have DISPLAY environment variable set in your host shell to this value.
  5. Run docker as shown in the example above from a command prompt

A future web-based version of COSMOS is planned that will remove the requirements of running an XServer and will greatly reduce the size of the COSMOS images.

## Running this image with a local COSMOS configuration

```console
docker run --rm -e DISPLAY -e QT_X11_NO_MITSHM=1 -v C:/git/cosmos/install:/cosmos ballaerospace/cosmos
```

To run with a COSMOS configuration stored on the Docker host, mount in your configuration to /cosmos as shown above.  Note: You may need to first delete the Gemfile.lock file from the COSMOS configuration folder.

## Image assumptions

By default these images should build and work without any major assumptions.

If you are behind a corporate firewall, the images support passing in a local certificate chain using Docker build secrets, that may allow them to build correctly on your network.  For this to work, you must be running docker with "Dameon -> Experimental features" enabled.

Then you should be able to build with a command similar to this:

```console
# Linux / Mac
DOCKER_BUILDKIT=1 docker build --secret=id=sslsecret,src=/certpath/cert.pem .

# Windows
set DOCKER_BUILDKIT=1
winpty docker build --secret=id=sslsecret,src=C:/certpath/cert.pem .
```

Note that on Windows, we recommend running docker through winpty.  It seems to fix the tty output and prevent container lockups especially when editing files in vi and accidently pressing multiple keys simulataneously on the keyboard.

# Image Variants

The `cosmos` images support a few different base images and tags:

## `ballaerospace/cosmos:<version>`

This is the defacto image. If you are unsure about what your needs are, you probably want to use this one. It is designed to be used both as a throw away container (mount your COSMOS configuration and start the container to start your app), as well as the base to build other images off of.

Some of these tags may have names like ubuntu or centos in them. These are the image names for releases of [Ubuntu](https://ubuntu.com/) and [Centos](https://www.centos.org/) that indicate which release the image is based on. If your image needs to install any additional packages beyond what comes with the image, you'll likely want to specify one of these explicitly to minimize breakage when there are new releases of a image.

The default cosmos image is currently based on Ubuntu but Centos is also supported.

## `ballaerospace/cosmos:<version>-ubuntu18.04`

COSMOS built against Ubuntu 18.04.  This is default base image.

## `ballaerospace/cosmos:<version>-centos7`

COSMOS built against Centos7.  Centos8 is not currently supported because it lacks a Qt4 package.

# License

View [license information](https://github.com/BallAerospace/COSMOS/blob/master/LICENSE.txt) for licensing specific to COSMOS itself.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.
