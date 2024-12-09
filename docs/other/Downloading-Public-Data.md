# Downloading Public NDA Data from NIMH Data Archive

## **1) Introduction to NDA**

Central hub for accessing large neuroimaging datasets, such as HCP.

**Website:**
https://nda.nih.gov/

**Example:**
HCP data: https://www.humanconnectome.org/storage/app/media/documentation/HCP-EP1.1/HCP-EP_Release_1.1_Manual.pdf 

## 2) Steps to Access and Download Data
**Step 1: Sign In**

* Navigate to **NDA** website.
* Click **My Account** → **Sign In with RAS**.
* Select **login gov** 

**Step 2: Get Study Data**

* Go to **Get Data**.
* Select **Connectomes Related to Human Disease** from the left bar.
* Choose **Access Shared Data Package**.
* Note your Data **Package ID**.

##  3) Command-Line Download Setup

**Prerequisite: Install Miniconda**

`Sudo su clevis`

```
#install Miniconda
mkdir ~/conda; cd ~/conda

wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

**Optional: Avoid base env auto-activation:**

 ```
export PATH="/home/clevis/miniconda3/bin:$PATH"
conda config --set auto_activate_base false
```
**Install nda-tools:**
```
#Install nda-tools
conda create -n nda-tools pip
conda activate nda-tools
pip install nda-tools
```

## 4) Download Data Using Terminal

**Command Structure:**

```
downloadcmd -dp <studyid> -u <NDA ID> -p <password> -d <directory you want to save data>
```

**Example:**

```
downloadcmd -dp  1190786  -u ghazale  -d /scratch/clevis/NDA
```

* Enter your NDA password when prompted.

**Keyring Error Fix:** pip install keyrings.alt

* Avoid saving directly in /external due to permission issues.

