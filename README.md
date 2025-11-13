# **🦖 Ark: Survival Ascended Dedicated Server (Docker \+ Wine)**

This project provides a highly stable, crossplay-enabled Docker container for running an ARK: Survival Ascended dedicated server on Linux. It uses **Wine** to run the Windows executable and includes critical stability fixes for **kernel limits (sysctls)** and **mod stability (PreventHibernation=True)**.

## **🚀 Quick Start Guide**

This setup requires Docker and Docker Compose (V2) to be installed on your host machine.

### **1\. Preparation**

1. **Clone the Repository:**  
   git clone \[https://github.com/iceyman/ark-server-docker.git\](https://github.com/iceyman/ark-server-docker.git)  
   cd ark-server-docker

2. **Create Data Directory:**  
   mkdir serverdata

3. **Configure:** Open docker-compose.yml and modify the environment variables (e.g., SERVER\_NAME, SERVER\_PASSWORD, MOD\_LIST).

### **2\. Launching the Server**

The image will be automatically pulled from the GitHub Container Registry (GHCR) when you run the launch command.

docker compose up \-d

## **⚙️ Configuration & Customization**

All settings are controlled via the environment section in docker-compose.yml.

| Variable | Default | Description |
| :---- | :---- | :---- |
| **SERVER\_NAME** | "My Awesome ASA Server" | Name displayed in the server list. |
| **SERVER\_PASSWORD** | "" | Password required to join the server. |
| **SERVER\_ADMIN\_PASSWORD** | "supersecretadminpassword" | Password for RCON and in-game admin commands. |
| **SERVER\_MAP** | "TheIsland" | Map to load (e.g., Svartalfheim). |
| **MAX\_PLAYERS** | 20 | Maximum player slots (fixed to avoid crossplay conflicts). |
| **MOD\_LIST** | "939228,928597" | **Required:** Comma-separated CurseForge Mod IDs. |
| **SERVER\_MODE** | PVP | Server ruleset (PVP or PVE). |
| **ADDITIONAL\_ARGS** | "-crossplay \-ForceModUpdate ?PreventHibernation=True" | **CRITICAL FLAGS** for stability and mod function. Do not remove. |

### **Game Rate Multipliers**

Adjust these variables (all default to 1.0 for standard rates):

* XP\_MULTIPLIER  
* HARVEST\_MULTIPLIER  
* TAMING\_SPEED  
* HATCHING\_SPEED  
* MATURATION\_SPEED

### **RCON & Ports**

The server uses network\_mode: host for simplicity, meaning the ports map directly to your machine's IP.

| Variable | Default | Protocol | Purpose |
| :---- | :---- | :---- | :---- |
| GAME\_PORT | 7777 | UDP | Game Client Connection |
| QUERY\_PORT | 27015 | UDP | Server List/Steam Query |
| RCON\_PORT | 27020 | TCP | Remote Administration Tools |

### **Advanced Config (Game.ini)**

For settings not available via launch arguments (e.g., custom stack sizes, decay timers), edit the files in your local serverdata volume:

* **serverdata/ShooterGame/Saved/Config/WindowsServer/Game.ini**  
* **serverdata/ShooterGame/Saved/Config/WindowsServer/GameUserSettings.ini**

## **⚠️ Troubleshooting**

| Problem | Cause | Solution |
| :---- | :---- | :---- |
| **Mods are not loading** | Host firewall blocking outbound connection to CurseForge. | Check your host's firewall rules (e.g., UFW/IPTables) to ensure the container can reach the public internet (port 443). |
| **Server hangs/crashes** | Kernel limits exceeded (vm.max\_map\_count). | This is fixed by sysctls in docker-compose.yml. If issues persist, ensure your host kernel allows this change. |
| **Lag/Desync when nobody is around** | Map Hibernation. | This is fixed by the ?PreventHibernation=True flag in ADDITIONAL\_ARGS. |
| **"Permission denied"** | Docker user (iceyman) cannot execute the deploy script. | Run chmod \+x deploy\_ghcr.sh and then try again. |

