# **🐳 Ark: Survival Ascended (ASA) Server on Linux (Docker/Wine)**

This project provides a robust, pre-configured Docker setup to run a dedicated **Ark: Survival Ascended** server on Linux using Wine, supporting crossplay, custom rates, and reliable CurseForge mod loading.

## **✨ Features**

* **Multi-Platform Support:** Runs on any Linux host with Docker installed.  
* **Crossplay Enabled:** Supports players joining from Steam and Epic Games (PC only).  
* **Automatic Mod Loading:** Automatically downloads and activates CurseForge mods using redundant, highly reliable methods (INI file injection and launch arguments).  
* **High Stability:** Includes critical kernel fixes (vm.max\_map\_count) and flags (?PreventHibernation=True) for long-term server stability.  
* **Full Customization:** Easy control over ports, rates, server name, passwords, and PvP/PvE mode via environment variables in docker-compose.yml.

## **🛠️ Prerequisites**

Before starting, ensure your host system meets these requirements:

1. **Docker & Docker Compose:** Installed and operational.  
2. **Git:** Installed for cloning the repository.  
3. **CRITICAL Kernel Setting:** Due to running the Windows game executable via Wine, the host Linux kernel requires a higher limit for memory map areas. **Run this command on your host machine:**  
   sudo bash \-c 'echo "vm.max\_map\_count \= 16777216" \> /etc/sysctl.d/20-max\_map\_count.conf'  
   sudo sysctl \--system

   *This is non-negotiable for stability.*

## **🚀 Setup and Launch**

### **1\. Clone the Repository (No Build Required)**

Users only need to clone the repository to get the configuration files, as the server image is pre-built and available on GHCR.

git clone \[https://github.com/iceyman/ark-survival-ascended-linux-container-image-docker.git\](https://github.com/iceyman/ark-survival-ascended-linux-container-image-docker.git)  
cd ark-survival-ascended-linux-container-image-docker

### **2\. Configure docker-compose.yml**

Edit the docker-compose.yml file to set up your server. The most important variables are listed below.

\# docker-compose.yml  
\# ... (inside the environment section)

      \# \--- General Server Settings \---  
      \- SERVER\_NAME="My Awesome ASA Server"  
      \- SERVER\_PASSWORD=""  
      \- SERVER\_ADMIN\_PASSWORD="supersecretadminpassword"  
      \- SERVER\_MAP="TheIsland"  
      \- MAX\_PLAYERS=20

      \# \--- Mod and Game Mode \---  
      \- MOD\_LIST="939228,928597"  \# Replace with your CurseForge Mod IDs (comma-separated)  
      \- SERVER\_MODE=PVP          \# Change to PVE for Player vs. Environment

      \# \--- Port Configuration \---  
      \- GAME\_PORT=7777           \# UDP Port for game connection  
      \- QUERY\_PORT=27015         \# UDP Port for server list query (Steam/Epic)  
      \- RCON\_PORT=27020          \# TCP Port for RCON tools

      \# \--- Rate Multipliers \---  
      \- XP\_MULTIPLIER=1.0        \# Increase for faster XP gain  
      \- HARVEST\_MULTIPLIER=1.0   \# Increase for faster resource gathering  
      \# ... and others like TAMING\_SPEED

### **3\. Launch the Server**

Run the server in the background: **Docker will automatically pull the image from GHCR** (ghcr.io/iceyman/...).

docker compose up \-d

On first run, the server will download the entire game (100GB+) and all mods. This process may take a long time depending on your internet connection.

## **⚙️ Customization Variables**

All server parameters are controlled via environment variables in docker-compose.yml.

| Variable | Default | Description |
| :---- | :---- | :---- |
| SERVER\_NAME | "My Awesome ASA Server" | Name displayed in the server list. |
| SERVER\_PASSWORD | "" | Password required to join. Leave blank for public. |
| MAX\_PLAYERS | 20 | Maximum number of players allowed. |
| MOD\_LIST | "" | Comma-separated list of **CurseForge Mod IDs**. |
| SERVER\_MODE | PVP | Set to PVE to enable Player vs. Environment rules. |
| XP\_MULTIPLIER | 1.0 | Global Experience Multiplier. |
| HARVEST\_MULTIPLIER | 1.0 | Global Resource Harvest Multiplier. |
| TAMING\_SPEED | 1.0 | Taming Speed Multiplier. |

### **Advanced Config (Game.ini)**

The script automatically creates a Game.ini and GameUserSettings.ini in the mounted ./serverdata/ShooterGame/Saved/Config/WindowsServer/ directory.

You can modify these files *while the server is stopped* to add advanced settings (e.g., custom structure limits, stack sizes, decay timers).

## **⚠️ Troubleshooting**

| Problem | Cause | Solution |
| :---- | :---- | :---- |
| **Mods don't load** | The container is blocked from accessing CurseForge download servers. | **Check your Host Firewall** (UFW/IPTables). Ensure outbound HTTPS traffic (Port 443\) from the Docker container is allowed. |
| **Server crashes on startup/heavy load** | Insufficient kernel memory mapping. | Ensure you ran the **CRITICAL Kernel Setting** command (Method 1 in Prerequisites) on your host machine. |
| **Max Players is stuck** | Conflict between ARK's internal and WinLive player limits. | This is fixed in the current version. Ensure you are using the latest install\_and\_run.sh which dynamically sets \-WinLiveMaxPlayers. |

