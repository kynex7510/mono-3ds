# mono-3ds

## Setting up the AOT environment

Requires [docker](https://www.docker.com/).

1. Spawn a terminal under `Container`
2. Build the docker image: `docker build -t mono-3ds .`

## Building AOT binaries

1. Spawn a terminal under `Container`
2. Move your assemblies under `Container/root`
3. Run the container: `docker run -v $PWD:/container --rm -it mono-3ds /bin/bash`
4. Invoke mono: `qemu-arm-static /opt/mono/bin/mono --aot=tool-prefix="/opt/armv6/bin/arm-linux-musleabihf-" /container/MyApp.exe`