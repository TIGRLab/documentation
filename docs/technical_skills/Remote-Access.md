# Remote Access

There are two main methods for remote access into the lab. The least laggy and most versatile is the ssh tunnel. But the easiest to set up is to use the https://remote.camh.ca based methods described below.

If you are missing accounts listed as a requirement for an access method below you can see instructions [here on our admin wiki](https://github.com/TIGRLab/admin/wiki/Setting-Up-Lab-Access) for requesting any accounts you may need (you must login to github to view this page).

If you need to find the name or IP of a workstation to connect to, you can see a complete list on [this page](https://github.com/TIGRLab/admin/wiki/IP-Addresses).

## SSH Tunnel

**Required Accounts:** Lab account, SCC Access, SciNet account

Once you have all the required accounts, you can set up the ssh tunnel by following these instructions:

  - [Mac/Linux](https://github.com/TIGRLab/admin/wiki/SciNet)
  - [Windows](https://github.com/TIGRLab/admin/wiki/Windows-MobaXTerm)

Note that you must be logged in to github to view either of those links.

## remote.camh.ca

**Required Accounts:** CAMH Account, Lab account, SCC Access
**Required Software:** [Citrix Receiver](https://www.citrix.com/products/receiver/)

1. Go to https://remote.camh.ca
2. Login with your CAMH account
3. Once in, in the 'non-clincal' folder you'll find a shortcut for Google Chrome. If you double click this, and have Citrix Receiver properly installed, it will open a chrome browser that runs on CAMH's internal network.
4. Choose one of the sections below to complete your login.

#### Jupyterhub

If you need a terminal or plan to use python you can follow these steps to access the jupyterhub.

- In the Chrome window you opened by following the steps above, go to https://jupyter.camh.ca/hub/login
- Log in with your SCC/TIGRlab account
- Once you're in you should see a 'new' button on the top right. If you click this, at the bottom of the menu there is a 'terminal' option. Clicking this will open a terminal on the SCC.

![](../../_images/jupyter_terminal.png)

- From here you can work on the SCC or you can ssh into a lab computer with `ssh yourkimelusername@yourmachinename.camhres.ca`

NOTE1: If you ever need to view files graphically (eg. html files from a QC pipeline's output), you can navigate to your home directory and symlink them there.
Eg.

```
cd . # (without being SSH-ed into a kimel lab computer)
ln -s /KIMEL/tigrlab/scratch/kimel_user/copied_folder .
```

NOTE2: If you're on a mac you may find that certain keyboard characters (like the '@' symbol) dont work correctly over Citrix. You may be able to fix this with the following two commands. You'll have to restart Citrix after these changes for them to take effect.

```
cd $HOME/Library/Application\ Support/Citrix\ Receiver
sed -e 's/KeyboardLayout=(User Profile)/KeyboardLayout=US/' -i SAVE Config
```

#### The QC Dashboard

If you just need to do scan QC, once you've opened a chrome browser on the network you can open the lab's QC dashboard by going to http://srv-dashboard.camhres.ca/

#### RStudiohub

If you want a terminal and/or plan to use rstudio then, after opening a chrome browser on the network by following the steps above, go to https://rstudio.camh.ca and login with your scc/lab account.

NOTE: You can follow the same symlinking procedure as in the Jupyterhub access section through the "terminal" tab on rstudio to copy your files over to the current directory so you can work on them

<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| TIGRLab | April 24th, 2023 | Needs update. |
| Dawn | May 12th, 2023 | Updated contents. |
<!-- sign-off-sheet:end -->
