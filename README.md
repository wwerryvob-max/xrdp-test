# 🚀 Headless Debian 11 XRDP + XFCE on GitHub Actions

A robust, lightning-fast, and persistent GUI environment hosted inside an isolated **Debian 11** Docker container on GitHub Actions. Bypasses the default GitHub runner D-Bus limitations and provides seamless remote access with automated cloud backups.

---

## ✨ Features

| Feature | Details |
| :--- | :--- |
| ⚡ **Native Docker Performance** | Runs inside a clean `debian:11` container at full native hardware speeds, completely solving D-Bus and XFCE failsafe loop issues. |
| 🖥️ **Full Desktop Environment** | Lightweight XFCE4 desktop with Firefox ESR pre-installed. |
| 🔒 **Secure Tunnel Access** | Access your desktop via Cloudflare Tunnel — no open ports required. |
| 💾 **Dual-Cloud Backup** | Choose Google Drive or MEGA to persist your `/root` directory across restarts (browser profiles, files, desktop layouts included). |
| ⏰ **Smart Incremental Sync** | Only uploads changed/new files and mirrors file deletions to the cloud every **1 hour** using `rclone sync`. |
| 🔄 **Auto-Restart Loop** | Automatically triggers the next run on completion so your desktop stays alive persistently. |

---

## 🔑 GitHub Secrets Configuration

Go to **Settings ➡️ Secrets and variables ➡️ Actions ➡️ New repository secret** and add the following:

| Secret Name | Required | Description |
| :--- | :---: | :--- |
| `PASS` | ✅ Yes | Password for the `root` user on the RDP desktop. |
| `CF_TOKEN` | ✅ Yes | Your Cloudflare Tunnel token to securely expose the session. |
| `GDRIVE_CONF` | ⭐ Optional | Rclone config block for Google Drive. **Takes priority over MEGA if both are set.** |
| `MEGA_USER` | ⭐ Optional | Your MEGA email address. Used only if `GDRIVE_CONF` is not set. |
| `MEGA_PASS` | ⭐ Optional | Your MEGA account password. Required alongside `MEGA_USER`. |

> **Backup Priority:** Google Drive is used first. If `GDRIVE_CONF` is not set, MEGA is used. If neither is configured, the session runs without backups.

---

## 💾 Cloud Backup Setup

### ☁️ Option A — Google Drive *(Recommended)*

1. Download and install **[Rclone](https://rclone.org/downloads/)** on your local PC.
2. Open a terminal and run:
   ```bash
   rclone config
   ```
3. Create a new remote — name it exactly **`gdrive`**, select **`drive`** (Google Drive) as the type, and follow the on-screen authentication flow.
4. Once done, open your `rclone.conf` file:
   - **Windows:** `C:\Users\<YourName>\AppData\Roaming\rclone\rclone.conf`
   - **macOS/Linux:** `~/.config/rclone/rclone.conf`
5. Copy the entire `[gdrive]` block and paste it as the value of the **`GDRIVE_CONF`** GitHub secret.

#### 📋 Example `GDRIVE_CONF` Secret Value:
```ini
[gdrive]
type = drive
client_id = your_client_id.apps.googleusercontent.com
client_secret = your_client_secret_here
scope = drive
token = {"access_token":"ya29.xxxxxxxxxxxx","token_type":"Bearer","refresh_token":"1//xxxxxxxxxxxx","expiry":"2026-07-05T12:00:00.000Z"}
```

> ⚠️ The remote **must** be named `[gdrive]` for the workflow to detect it correctly!

---

#### 📱 On Mobile (Android via Termux)

> [!TIP]
> You can set up Google Drive backup entirely from your Android phone — no PC required!

**Step A: Get the App**

1. Download **Termux** from the [F-Droid store](https://f-droid.org/en/packages/com.termux/) — ⚠️ **do NOT use the Google Play version**, it is outdated and broken.
2. Open Termux and run:
   ```bash
   pkg update && pkg upgrade -y
   pkg install rclone -y
   ```

**Step B: Link Your Google Account**

3. Start the rclone setup wizard:
   ```bash
   rclone config
   ```
4. Type `n` (New remote) and name it exactly **`gdrive`**.
5. A large list of cloud providers appears. Find **Google Drive** (usually option `18` or `19`) and type that number.
6. Leave `client_id` **blank** → press Enter.
7. Leave `client_secret` **blank** → press Enter.
8. For `scope`, type `1` (Full access to all files).
9. Leave `root_folder_id` and `service_account_file` **blank** → press Enter both times.
10. `Edit advanced config?` → type `n`.
11. `Use web browser to authenticate?` → type `y`. It will output a URL starting with `http://127.0.0.1:53682/auth...`.
12. Copy that URL and open it in **Chrome** on your phone.
13. Log into your Google account and click **Allow**.
14. Back in Termux, when asked `Configure as Shared Drive?` → type `n`.
15. When asked if the config is OK → type `y`, then `q` to quit.

**Step C: Extract and Copy the Config**

16. Print your config to the screen:
    ```bash
    cat ~/.config/rclone/rclone.conf
    ```
17. Long-press to **select and copy** the entire `[gdrive]` block.
18. Paste it as the value of the **`GDRIVE_CONF`** GitHub secret.

---

### 🟠 Option B — MEGA *(Easiest Setup)*

No local software required. Just add your MEGA login credentials as GitHub secrets:

1. Add your MEGA email as the **`MEGA_USER`** secret.
2. Add your MEGA password as the **`MEGA_PASS`** secret.

The workflow automatically configures `rclone` for MEGA on the fly.

---

## 🗂️ What Gets Backed Up?

The backup syncs your entire **`/root`** directory, which includes:
- 📁 Desktop files and folders
- 🌐 Firefox browser profiles (bookmarks, history, cookies, saved passwords)
- 🐍 Python scripts, `.txt` files, and any other files you create
- ⚙️ Application configurations stored in `/root`

System files, installed packages, and caches are **excluded** to keep backups fast and small.

