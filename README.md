# ARK: Survival Ascended (ASA) Docker Server

[![Docker](https://img.shields.io/badge/Docker-blue?style=for-the-badge&logo=docker)](https://www.docker.com/)
[![Linux](https://img.shields.io/badge/Linux-black?style=for-the-badge&logo=linux)](https://www.linux.org/)
[![Wine](https://img.shields.io/badge/Wine-white?style=for-the-badge&logo=wine)](https://www.winehq.org/)

This repository contains the necessary files to build and run a stable, high-performance dedicated server for **ARK: Survival Ascended (ASA)** using Docker.

The server runs the official Windows server files on a Linux host using Wine, all managed within a container. This setup is pre-configured to handle common stability issues, mod support, and provides a simple way to manage your server using environment variables.

## 🚀 Features

* **Fully Containerized:** Runs the entire server, including Wine, in a single Docker container.
* **Auto-Updating:** Server (and mods) are validated and updated by SteamCMD on every container start.
* **Easy Configuration:** All server settings, including name, passwords, and multipliers, are managed via the `docker-compose.yml` file.
* **Mod Support:** Simply provide a comma-separated list of CurseForge Mod IDs.
* **Stability Patched:** Includes logic to fix common ASA on Wine stability issues and client-side bugs (like the session name display).
* **Health Check:** The `docker-compose.yml` includes a health check to ensure Docker knows when your server is *actually* running.

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

> **Note:** The included `docker-compose.yml` has the `sysctls` block commented out. If your host kernel is modern (5.4+), you can try uncommenting it. If you get an error, use the host machine method above and keep it commented.

## 🏁 Quick Start for Users

This will get your server running using the pre-built Docker image.

1.  **Clone this Repository:**
    ```bash
    git clone [https://github.com/iceyman/ark-server-docker.git](https://github.com/iceyman/ark-server-docker.git)
    cd ark-server-docker
    ```

2.  **Ensure Prerequisites:** (See the `vm.max_map_count` setup above).

3.  **Edit `docker-compose.yml`:**
    Open the `docker-compose.yml` file and edit the `environment` variables to your liking. At a minimum, you **must** change:
    * `SERVER_NAME`
    * `SERVER_PASSWORD`
    * `SERVER_ADMIN_PASSWORD`

4.  **Pull the latest Image:**
    This downloads the pre-built server image.
    ```bash
    docker-compose pull
    ```

5.  **Start the Server:**
    ```bash
    docker-compose up -d
    ```

Your server will now start in the background. The *first startup* will take a long time (15-30+ minutes) as it needs to download and install the entire ARK server. Subsequent starts will be much faster.

You can view the server's startup progress and logs by running:
```bash
docker-compose logs -f

🔧 Server Configuration (Environment Variables)
All server configuration is handled in the environment: block of your docker-compose.yml file.

Variable,Default Value,Description
Essential,,
SERVER_NAME,"""Iceys Dino Adventures""",The name your server will display in the server list.
SERVER_PASSWORD,"""pimylifeup""",The password players must use to join. Leave blank for no password.
SERVER_ADMIN_PASSWORD,"""iceymidgit007""",The password used to gain admin rights in-game.
SERVER_MAP,"""TheIsland_WP""",The map to load.
SERVER_MODE,"""PvE""","Set to ""PvE"" or ""PvP""."
MAX_PLAYERS,20,Maximum number of players allowed on the server.
Ports,,
GAME_PORT,7777,Must match the first port in the ports: section.
QUERY_PORT,27016,Must match the second port in the ports: section.
RCON_PORT,27020,Must match the third port in the ports: section.
Mods & Advanced,,
MOD_LIST,(example list),Comma-separated list of CurseForge Mod IDs.
ADDITIONAL_ARGS,"""-crossplay -ForceModUpdate &PreventHibernation=True""","Advanced launch arguments. Use & for map args, flags start with -."
Rates & Multipliers,,
TAMING_SPEED,5.0,Taming speed multiplier.
HARVEST_MULTIPLIER,3.0,Harvest amount multiplier.
XP_MULTIPLIER,2.0,Experience multiplier.
MATURATION_SPEED,10.0,Baby maturation speed.
HATCHING_SPEED,10.0,Egg hatching speed.
BABY_FOOD_CONSUMPTION,0.5,Baby food consumption (lower is slower).
BABY_CUDDLE_INTERVAL,0.1,Time between baby imprinting cuddles (lower is faster).
BABY_IMPRINTING_SCALE,1.0,Stat bonus scale from imprinting.
BABY_IMPRINT_AMOUNT,1.0,Imprint amount per cuddle.
BABY_STAMINA_GAIN,1.0,Stamina gain multiplier for babies.
