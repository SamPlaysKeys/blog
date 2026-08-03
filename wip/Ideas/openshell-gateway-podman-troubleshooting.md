# OpenShell Gateway + Podman on macOS — Brew Service Fix

## Symptoms

`brew services list` shows `openshell error 1`. The error log (`/opt/homebrew/var/log/openshell/openshell-gateway.err.log`) contains:

```
configuration error: no compute driver configured and auto-detection found no suitable driver
```

or after setting the driver env var:

```
failed to create compute runtime: connection error: ...podman.sock: No such file or directory
```

## Root Cause (Two Layers)

### Layer 1: Missing `OPENSHELL_DRIVERS`

OpenShell's brew LaunchAgent inherits **zero environment** from your shell. It doesn't see `OPENSHELL_DRIVERS` even if you `export` it. The service wrapper at `$(brew --prefix openshell)/libexec/openshell-gateway-homebrew-service` supports a `.env` file but won't find one if it doesn't exist.

### Layer 2: Stale Podman Socket

Podman on macOS 6.x connects to its Linux VM via SSH. The local API socket lives in a macOS temp directory (`/var/folders/...`) that gets cleaned on reboot or by the system. The symlink at `~/.local/share/containers/podman/machine/podman.sock` becomes a dead link.

## Fix

### 1. Create the env file

```sh
mkdir -p ~/.config/openshell
echo 'OPENSHELL_DRIVERS=podman' > ~/.config/openshell/gateway.env
```

This is sourced automatically by `openshell-gateway-homebrew-service` (lines 12–18 of the script).

### 2. Restart the podman machine (recreates socket)

```sh
podman machine stop && podman machine start
```

### 3. Restart OpenShell

```sh
brew services restart openshell
```

## Verification

```sh
brew services list                # should say "started"
cat /opt/homebrew/var/log/openshell/openshell-gateway.out.log | grep "Connected to Podman"
curl -sk https://127.0.0.1:17670/health | jq     # optional, if health endpoint is enabled
```

Expected output includes:
```
Using compute driver driver=podman
Connected to Podman
Server listening address=127.0.0.1:17670
```

## Prevention

- **After every macOS reboot or `/tmp` cleanup:** run `podman machine start` then `brew services restart openshell`.
- **The `gateway.env` file only needs to be created once.** It persists across reboots.
- If you switch to Docker later, change the value to `OPENSHELL_DRIVERS=docker` in that same file.
- The podman socket path can be overridden with `OPENSHELL_PODMAN_SOCKET` if needed (check `podman machine inspect --format '{{.ConnectionInfo.PodmanSocket.Path}}'`).

## One-liner to confirm the full chain

```sh
test -S ~/.local/share/containers/podman/machine/podman.sock && \
  curl --unix-socket ~/.local/share/containers/podman/machine/podman.sock http://localhost/_ping
```

Should return `OK`.
