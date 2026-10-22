
# Container

Apple's native container tool for running Linux containers on macOS (available from macOS 26).


## Update

```bash
container system stop
/usr/local/bin/update-container.sh
```


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

```bash
container machine create debian13:latest --platform linux/amd64 --memory 16G --cpus 8  --name develop
```

Quick User Setup

```bash
mkdir -p ~/.ssh/ ; chmod 700 ~/.ssh/ ; cp /Users/`(whoami)`/.ssh/authorized_keys ~/.ssh/
```

#MACOS

## SSH into a Container Machine

Use `container machine run` — not plain `ssh` — as a `ProxyCommand`. Apple's `container` vmnet NAT has a bug where it drops SSH connections (the TCP handshake succeeds but sshd inside the VM never receives the connection). `nc` and `container machine run` work because they use a direct vsock/virtio path instead of the NAT.

Add to `~/.ssh/config`:

```
Host develop
  HostName 192.168.64.4
  ProxyCommand container machine run -n develop --root --interactive -- nc localhost 22
  RemoteForward 6655 localhost:6655
```

Then connect normally:

```bash
ssh develop
```

### Symptom

```
kex_exchange_identification: read: Bad file descriptor
banner exchange: Connection to 192.168.64.2 port 22: Bad file descriptor
```

Or after first retry:

```
kex_exchange_identification: read: Socket is not connected
```

`nc 192.168.64.2 22` reads the SSH banner fine — the server is healthy. Plain `ssh` fails because macOS OpenSSH uses a non-blocking socket connect that the vmnet NAT mishandles.

### Diagnosis

- `nc` to port 22 → banner received every time ✅
- `ssh` to port 22 → dies at banner read every time ❌
- `journalctl _COMM=sshd` inside the VM → no connection attempts logged (connections never reach sshd)
- Fix: route ssh through `container machine run --interactive -- nc localhost 22`
