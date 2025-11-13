# **⚓ ASA Dedicated Server (Docker/Wine)**

A highly stable, crossplay-enabled **Ark: Survival Ascended (ASA)** dedicated server running on Linux via **Docker and Wine**. This solution resolves common stability issues and simplifies deployment and configuration.

## **✨ Features**

* **One-File Setup:** All configuration in a single docker-compose.yml.  
* **Stability Fixes:** Includes the necessary kernel sysctls fix to prevent crashes in a Linux/Wine environment.  
* **Dynamic Configuration:** Customize rates, passwords, map, and mode via environment variables.  
* **Mod Support:** Automated download and setup for CurseForge Mod IDs.  
* **Crossplay:** Fully configured for both Steam and Xbox/Microsoft Store users.  
* **Self-Maintenance:** Automatic daily backups and cleanup of server configuration files.  
* **Access Control:** Easy player whitelisting via the Whitelist.txt file.

## **🚀 Quick Start (Using Pre-built Image)**

This guide assumes you have **Docker** and **Docker Compose (v2)** installed on your Linux server.

### **1\. Preparation**

1. **Clone the repository:**  
   git clone \[https://github.com/iceyman/ark-server-docker.git\](https://github.com/iceyman/ark-server-docker.git)  
   cd ark-server-docker

2. **Configure docker-compose.yml:**  
   * Edit the docker-compose.yml file and customize all the environment variables (especially SERVER\_NAME, SERVER\_ADMIN\_PASSWORD, and MOD\_LIST).  
3. **Create Volume:** The game files and saved data will be stored in a local directory named serverdata. Create this folder:  
   mkdir serverdata

### **2\. Launching the Server**

The server uses the pre-built image from GHCR, so there is no local build step.

1. **Start the container:**  
   docker compose up \-d

   This command will pull the latest stable image (ark-server-docker:latest), create the volume, and start the server.  
2. Monitor the Startup:  
   Check the logs. The first run will take a significant amount of time as it downloads the 80GB+ game files via SteamCMD.  
   docker compose logs \-f

## **⚙️ Configuration Details**

All essential settings are managed via the environment block in docker-compose.yml.

### **Ports**

| Environment Variable | Default | Purpose |
| :---- | :---- | :---- |
| GAME\_PORT | 7777 | **Primary Game Port** (connects players) |
| QUERY\_PORT | 27015 | **Steam Query Port** (server list visibility) |
| RCON\_PORT | 27020 | **RCON Port** (for management tools) |

**Note:** Since network\_mode: host is used, ensure your server's firewall (e.g., UFW) allows UDP traffic on these ports.

### **Mods**

Set the MOD\_LIST variable with comma-separated CurseForge IDs.

| Variable | Example | Description |
| :---- | :---- | :---- |
| MOD\_LIST | "939228,928597" | Add CurseForge Mod IDs here. Leave empty ("") to run vanilla. |

### **Access Control (Whitelisting & Bans)**

The server is configured to check for a Whitelist.txt file in your configuration volume (./serverdata/ShooterGame/Saved/Config/WindowsServer).

* **To Whitelist Players:**  
  1. Create the file inside your server volume: ./serverdata/ShooterGame/Saved/Config/WindowsServer/Whitelist.txt  
  2. Add one player Steam/Xbox ID per line (no commas, no names).  
  3. If this file contains any IDs, the server will automatically enable the whitelist.  
* To Ban Players:  
  The server also supports ./serverdata/ShooterGame/Saved/Config/WindowsServer/BanList.txt. Add one banned player ID per line.

## **🛠 Troubleshooting & Maintenance**

### **How to Update the Server**

To update the ASA game files to the latest version, simply restart the container. The startup script runs SteamCMD \+app\_update 2430930 validate every time.

docker compose restart

### **Server Lag / Lost Resources**

If you experience resource despawns, severe lag, or mods failing, this is often due to ARK's "hibernation" feature. This has been preemptively solved by the server's launch argument:

* ?PreventHibernation=True (In ADDITIONAL\_ARGS)

### **Deployment Commands**

If you make changes to the Dockerfile or install\_and\_run.sh, you must use the deploy\_ghcr.sh script to update the remote image:

\# Log in to GitHub Container Registry (only needed once)  
docker login ghcr.io \-u iceyman

\# Build and push both 'dev' and 'stable' tags  
./deploy\_ghcr.sh  
