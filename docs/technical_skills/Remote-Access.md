# Remote Access

There are two main methods for remote access into the lab. The least laggy and most versatile is the ssh tunnel. But the easiest to set up is to use the https://remote.camh.ca based methods described below.

If you are missing accounts listed as a requirement for an access method below you can see instructions here [INSERT]() for requesting any accounts you may need.

If you need to find the name or IP of a workstation to connect to, you can see a complete list on this page [INSERT]().

## SSH Tunnel

**Required Accounts:** Lab account, SCC Access, SciNet account

Divide these links up with separate instructions for linux/mac and windows.

To get into the lab computers through the ssh tunnel you'll first need to request the accounts listed on this page [INSERT]().

Once you have access to all three (our lab, the SCC and SciNet), follow the instructions on this page [INSERT]() to setup the your ssh configuration.

## remote.camh.ca

**Required Accounts:** CAMH Account, Lab account, SCC Access
**Required Software:** [Citrix Receiver](https://www.citrix.com/products/receiver/)

1. Go to https://remote.camh.ca
2. Login with your CAMH account
3. Once in, in the 'non-clincal' folder you'll find a shortcut for Google Chrome. If you double click this, and have Citrix Receiver properly installed, it will open a chrome browser that runs on CAMH's internal network.
4. Choose on of the sections below to complete your login.

### Jupyterhub

If you need a terminal or plan to use python follow these steps to complete your login through the jupyterhub.

5. In the Chrome window you opened by following the steps above, go to https://jupyter.camh.ca/hub/login
6. Log in with your SCC/TIGRlab account
7. Once you're in you should see a 'new' button on the top right. If you click this, at the bottom of the menu there is a 'terminal' option. Clicking this will open a terminal on the SCC.

![](../../_images/jupyter_terminal.png)

8. From here you can work on the SCC or you can ssh into a lab computer with `ssh yourkimelusername@yourmachinename.camhres.ca`

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

Now you will be able to view the files in your home directory on the jupyter hub sidebar. You can now open them in a new browser tab to look at them.

### The QC Dashboard

If you just need to do scan QC, once you've opened a chrome browser on the network you can open the lab's QC dashboard by going to http://srv-dashboard.camhres.ca/

### RStudio

If you want a terminal and/or plan to use rstudio then, after opening a chrome browser on the network by following the steps above, go to https://rstudio.camh.ca and login with your scc/lab account.

NOTE: You can follow the same symlinking procedure as in the Jupyterhub section through the "terminal" tab on rstudio to copy your files over to the current directory so you can work on them
