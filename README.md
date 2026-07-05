# Headless Ubuntu 24.04 XRDP + XFCE on GitHub Actions

This repository contains a GitHub Actions workflow to launch a stable, headless Ubuntu 24.04 desktop session via XRDP and XFCE. It includes a custom D-Bus initialization fix that runs XFCE in an isolated D-Bus session, preventing the "Unable to load a failsafe session" error without restarting system services (which would crash the runner).

---

## Required GitHub Secrets

To run this workflow, you need to add the following secrets in your repository settings (**Settings -> Secrets and variables -> Actions -> New repository secret**):

| Secret Name | Description |
| :--- | :--- |
| `PASS` | The password you want to use for the custom `RDP` user when logging into the desktop session. |
| `CF_TOKEN` | Your Cloudflare Tunnel token. Used by `cloudflared` to expose the XRDP session securely to the internet without opening ports. |

---

## Features

- **XFCE4 Desktop Environment**: Lightweight and stable desktop session.
- **Firefox Native ESR**: Pre-installed and patched to bypass sandbox issues inside virtualized GitHub runner environments.
- **Isolated D-Bus Configuration**: Solves the RDP failsafe loop without system service restarts.
- **Auto-Restart Loop**: Automatically triggers a new run when the current run finishes to maintain a persistent environment (if desired).
