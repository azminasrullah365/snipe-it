![logo](https://snipeitapp.com/img/logos/snipe-it-logo-xs.png)
# Snipe-IT
How to self host snipe it
<br>
| Resource | Recommended                       |
|:---:|:---:|
| OS       | Ubuntu Server 22.04 LTS           |
| CPU      | 2 vCPU (4 if many users)          |
| RAM      | 4 GB (2 GB works for small teams) |
| Storage  | 40–50 GB                          |
| Network  | Bridge (vmbr0)                    |
<br>

## Step 1 — System Preparation and Update
```
sudo apt update && sudo apt upgrade -y
sudo apt install -y software-properties-common unzip curl git
```
## Step 2 — Install Web Stack (LEMP - Recommended)
```
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg gi
```
###
```
sudo apt install -y mariadb-server
sudo mysql_secure_installation
```
