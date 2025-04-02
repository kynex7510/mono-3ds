# mono-3ds

## Setting up the AOT environment

Requires [docker](https://www.docker.com/).

1. Spawn a terminal under `Container`
2. Build the docker image: `docker build -t mono-3ds .`
3. Take a nap, as this will take a lot of time.

## Building AOT binaries

1. Move your assemblies under `Container`
2. Spawn a terminal under `Container`
3. Run the container: `docker run -v $PWD:/container --rm -it mono-3ds /bin/bash`
4. Invoke mono:

```
cd /container
qemu-arm-static /opt/mono/bin/mono --aot=tool-prefix="/opt/armv6/bin/arm-linux-musleabihf-" MyApp.exe
```