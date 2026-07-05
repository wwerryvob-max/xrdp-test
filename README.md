# Headless Debian 11 XRDP + XFCE on GitHub Actions

This repository contains a GitHub Actions workflow to launch a stable, headless Debian 11 desktop session via XRDP and XFCE inside a Docker container. Running the environment within an isolated Debian 11 container completely bypasses the custom D-Bus initialization restrictions of the default GitHub Ubuntu runner, preventing the "Unable to load a failsafe session" error.

---

## Required GitHub Secrets

To run this workflow, you need to add the following secrets in your repository settings (**Settings -> Secrets and variables -> Actions -> New repository secret**):

| Secret Name | Description |
| :--- | :--- |
| `PASS` | The password you want to use for the `root` user when logging into the desktop session. |
| `CF_TOKEN` | Your Cloudflare Tunnel token. Used by `cloudflared` to expose the XRDP session securely to the internet without opening ports. |

---

## Features

- **XFCE4 Desktop Environment**: Lightweight and stable desktop session.
- **Firefox Native ESR**: Pre-installed and patched to bypass sandbox issues inside virtualized GitHub runner environments.
- **Debian 11 Docker Isolation**: Runs in a clean, native `debian:11` container, completely solving the RDP failsafe loop and D-Bus crashing issues inherent to GitHub runners.
- **Auto-Restart Loop**: Automatically triggers a new run when the current run finishes to maintain a persistent environment (if desired).
