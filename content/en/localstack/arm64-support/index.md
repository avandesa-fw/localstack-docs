---
title: "ARM64 Support"
weight: 30
description: >
  Describes the support for the ARM64 CPU architecture in LocalStack.
tags: ["apple", "silicon", "m1", "raspberry pi"]
---

With [version 0.13](https://github.com/localstack/localstack/releases/tag/v0.13.0), LocalStack officially publishes a [multi-architecture Docker manifest](https://hub.docker.com/r/localstack/localstack).
This manifest contains links to a Linux AMD64 as well as a Linux ARM64 image.

{{% alert title="Experimental" color="warning" %}}
The ARM64 image of LocalStack is still experimental.
Help us getting aware of current issues with the ARM64 image by [filing an issue](https://github.com/localstack/localstack/issues/new?assignees=&labels=bug,ARM64%2Cneeds-triaging&template=bug-report.yml&title=bug%3A+%3Ctitle%3E) if you experience any problems.

Currently known limitations are collected in the GitHub issue [localstack/localstack#4921](https://github.com/localstack/localstack/issues/4921).
{{% /alert %}}

## Pulling the image
With the multi-arch Docker manifest, your Docker client (and therefore the [LocalStack CLI]({{< ref "get-started/#localstack-cli" >}})) now automatically selects the image according to your platform:
{{< command >}}
$ docker pull localstack/localstack
{{< / command >}}

You can check the architecture of the pulled image by using `docker inspect`:
{{< command >}}
$ docker inspect localstack/localstack | jq .[0].Architecture
"arm64"
{{< / command >}}

## Troubleshooting
### Pulling images for other architectures
{{% alert title="Unsupported" color="warning" %}}
Please be aware that this workaround is not supported by LocalStack at all.
{{% /alert %}}
If you want to use a LocalStack image which has been built for another architecture than yours, you can instruct Docker to use another platform by setting the `DOCKER_DEFAULT_PLATFORM` environment variable:
{{< command >}}
$ export DOCKER_DEFAULT_PLATFORM=linux/amd64
{{< / command >}}
When using Docker Compose, you can use the `platform` element [as described in the specification](https://github.com/compose-spec/compose-spec/blob/master/spec.md#platform).

### Apple Silicon / Apple M1
If you are experiencing issues with the ARM64 image (and after you created an issue to make us aware of the problem 😉), you can try to use the AMD64 packages on your Apple Silicon device and use Apple Rosetta to emulate the AMD64 / x86_64 CPU architecture.

{{% alert title="Unsupported" color="warning" %}}
Please be aware that this workaround is not supported by LocalStack at all.
{{% /alert %}}

First, you should enable "Rosetta" on your preferred terminal.
This way you'll be installing packages for `x86_64` platform.

![Rosetta](m1-trouble-1.png)

What we will be doing now is installing Java and Python executables using Homebrew, it should automatically resolve packages to proper architecture versions.

```bash
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install java11 and follow instructions
brew install java11

# Install jenv and follow instructions
brew install jenv

# Add Java11 to jenv and use it globally
jenv add /Library/Java/JavaVirtualMachines/openjdk-11.jdk/Contents/Home/
jenv global 11

# Install pyenv and follow instructions
brew install pyenv

# Install python 3.8.10 and enable it globally
pyenv install 3.8.10
pyenv global 3.8.10
```

Then clone LocalStack to your machine, run `make install` and then `make start`.

{{% alert title="Note on JVM Lambda" color="warning" %}}
You need to use the `local` lambda executor for JVM Lambda functions.
{{% /alert %}}


### Raspberry Pi
If you want to run LocalStack on your Raspberry Pi, make sure to use a 64bit operating system.
In our experience, it works best on a Raspberry Pi 4 8GB with [Ubuntu Server 20.04 64Bit for Raspberry Pi](https://ubuntu.com/download/raspberry-pi).

You can check if Docker is running and your architecture is ARM64 / aarch64 by using `docker info`:
{{< command "hl_lines=7-9" >}}
$ docker info
Client:
 ...

Server:
 ...
 Operating System: Ubuntu 20.04
 OSType: linux
 Architecture: aarch64
 ...
{{< / command >}}