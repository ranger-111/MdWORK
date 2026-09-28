
# Container

Apple's native container tool for running Linux containers on macOS (available from macOS 26).

## Setup

Start the container system:

```bash
container system start
```

Create DNS for the machine:

```bash
sudo container system dns create machine
```

## Build an Image

Build an image tagged `debian13` from a `Containerfile` in the current directory:

```bash
container build -t debian13 .
```

## Create a Machine

Create a Debian 13 container named `develop`:

```bash
container machine create debian13:latest --name develop
```

#MACOS
