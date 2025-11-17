# 🐳 ARK: Survival Ascended Dedicated Server - Dockerized


This project provides a robust, self-maintaining, and highly configurable Docker container for running an ARK: Survival Ascended (ASA) dedicated server on Linux using Wine.

This setup is pre-configured to handle common stability issues, mod support, and provides a simple way to manage your server using environment variables.

## ✨ Features

* **Fully Containerized:** Runs the entire server, including Wine, in a single Docker container.
* **Auto-Updating:** Server (and mods) are validated and updated by SteamCMD on every container start.
* **Easy Configuration:** All server settings, including name, passwords, and multipliers, are managed via the `docker-compose.yml` file.
* **Mod Support:** Simply provide a comma-separated list of CurseForge Mod IDs.
* **Stability Patched:** Includes logic to fix common ASA on Wine stability issues and client-side bugs (like the session name display).
* **Config Backups:** Automatically creates timestamped backups of your `.ini` files on every startup.
* **Automatic Cleanup:** Deletes config backups older than 30 days to save space.
* **Health Check:** A built-in health check ensures Docker knows when your server is *actually* ready to play.

## ⚠️ Prerequisites

Before you begin, you **must** have the following installed on your Linux host machine:
* [Docker](https://docs.docker.com/engine/install/)
* [Docker Compose](https://docs.docker.com/compose/install/)

### Critical Host Setup (vm.max_map_count)

ARK on Wine requires a higher-than-default `vm.max_map_count` value to avoid crashing. You must set this **on your host machine**.

1.  **Set the value temporarily:**
    ```bash
    sudo sysctl -w vm.max_map_count=262144
    ```
2.  **Set the value permanently** (so it survives a reboot):
    ```bash
    echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
    ```
> **Note:** If you get an `OCI runtime create failed... sysctl "vm.max_map_count" is not in a separate kernel namespace` error, it means your host kernel is too old. The steps above are the **required fix**.

## 🏁 Quick Start (For Server Admins)

Follow these steps to get your server running within minutes.

### 1. ⚙️ Preparation


Clone this repository to your server:
```bash
git clone [https://github.com/iceyman/ark-server-docker.git](https://github.com/iceyman/ark-server-docker.git)
cd ark-server-docker

2. 🚀 Launching the Server
Edit docker-compose.yml: Open the file and customize the environment variables. At a minimum, you must change SERVER_PASSWORD and SERVER_ADMIN_PASSWORD.

Pull the latest image:

Bash

docker-compose pull
Start the Server:

Bash

docker-compose up -d
The server will now start in the background. The first startup will take a long time (15-30+ minutes) as it needs to download and install the entire ARK server.

To view the server logs in real-time: docker-compose logs -f

To stop the server: docker-compose down

### **Essential Settings:**


| Variable | Description | Action Required |
| :--- | :--- | :--- |
| `SERVER_NAME` | Name displayed in the ARK server browser. | Customize |
| `SERVER_PASSWORD` | Password required to join the game. | **MUST CHANGE** |
| `SERVER_ADMIN_PASSWORD`| Password for in-game admin commands. | **MUST CHANGE** |
| `SERVER_MAP` | e.g., `TheIsland_WP`, `ScorchedEarth_WP`. | Customize |
| `SERVER_MODE` | Set to `"PvE"` or `"PvP"`. | Customize |
| `MAX_PLAYERS` | Maximum number of players allowed on the server. | Customize |
| `MOD_LIST` | Comma-separated list of CurseForge Mod IDs. | Customize |

### **Ports (Match the `ports:` section):**

| Variable | Description |
| :--- | :--- |
| `GAME_PORT` | `7777` (Game Client) |
| `QUERY_PORT` | `27016` (Steam Query) |
| `RCON_PORT` | `27020` (RCON Tools) |

### **Advanced Launch Arguments:**

| Variable | Description |
| :--- | :--- |
| `ADDITIONAL_ARGS`| Advanced launch arguments. Use `&` for map args (e.g., `&PreventHibernation=True`) and flags start with `-` (e.g., `-crossplay`). |
