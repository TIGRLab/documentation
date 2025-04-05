# Remote Access to the Kimel Lab Server using VS Code

This guide explains how to remotely connect to the Kimel Lab server using Visual Studio Code (VS Code).

VS Code is a powerful, IDE-like environment that supports a wide range of extensions to customize your experience and workflow. One particularly useful extension is Remote - SSH, which allows you to remotely access the Kimel cluster. With this extension, you can browse files, edit code, and use a terminal on the server—all within the VS Code interface. Combined with other extensions, this setup can significantly enhance your workflow.

---


## Table of Contents

1. [What You'll Need](#what-youll-need)   
2. [Setting Up VSCode](#setting_up_vscode)
    1. [Remote-SSH](#remote_ssh_vscode)
3. [Remoting in](#remoting_in)
4. [Navigating](#navigating)
5. [Extensions](#extensions)
    1. [Live Preview](#live_preview)
    2. [NiiVue](#niivuew)
    3. [Jupyter](#jupyter)




---

## What You'll Need

- [Visual Studio Code ](https://code.visualstudio.com/download) installed on your system.
- Remote access to the Kimel system, specifically a completed config file. With all the subsequent remote set up requirements. Such as, an Alliance Canada account, DUO 2-factor authentication set up, and the various CAMH/Kimel accounts.

Should you be missing any of the remote log in steps please refer to the [admin wiki](https://github.com/TIGRLab/admin/wiki/SciNet).
This guide will assume these steps have already been completed.

## Setting Up VSCode

Set up VSCode with all the recommended settings. Once you launch it, it should look very similar to image below. 

![image](../_images/vscode/vscode_main_page.png)

### Remote-SSH

The first and most important extension you will need to install is the Remote-ssh, if you take a look at the image below, click on the extensions tab on the left (where the pink arrow is pointing at), and search for "Remote-SSH" by "Microsoft" -- see below

![image](../_images/vscode/remote_ssh.png)

Once installed you should now see a new button(connect to host) on the bottom left portion of the window (where the yellow arrow is pointing). Clicking this button will open up a search box for you to select a few options, you're now going to set up your config file for vscode to be abel to tunnel through scinet and into the kimel cluster you're.

Click on "connect to a host" as part of the Remote-SSH package (pay attention to the right side of the options to tell you what package you're calling on)

![image](../_images/vscode/remote_ssh_pallete.png)

Once you do should be given a few options, as I've set my config file differently so you may or may not see the same options, regardless click on "configure SSH hosts"

![image](../_images/vscode/connect_to_host.png)

Now select the path to your computers ssh config file - the same one that you would typically use to ssh to scinet/kimel with. Open the config file and just take a look to see if you're able to see all the hosts, or to add and make changes to- any edits here *will* edit your actual config file too. 

Note that for the purpose of privacy this image is blurred, if you need instructions on how to set up the config file please reach out to a member of the TigrCats, or please refer to the very thorough [admin wiki](https://github.com/TIGRLab/admin/wiki/SciNet) that tells you how to do this.

Once you've verified your config is set up correctly, you should now see the lists of hosts in the previous step. But before we attempt to connect to one of the hosts, we need to ensure your connection timeout limit is set to something reasonable. Wherein, how many seconds do you allocate VSCode to attempt to connect to one of your hosts - by default its set to something ridiculously low, and because we have 2 factor authentication we need way more time that. 

Press **CTRL(or CMD) + SHIFT + P** -- and search up remote settings

![image](../_images/vscode/remote_settings.png)

Scroll down to "Remote-SSH Connect Timeout" and just add a 0 to the end of the numbers there, so in this case mine says **15**, I will add a 0 to make it **150**

![image](../_images/vscode/connection_timeout.png)

You're now ready to remote in!


##  Remoting In

At this point you should have all your ducks in a row to remote in, that means you should have a **complete and usable config file** (confirmed through your ssh on the terminal) and the **Remote-SSH extension installed**.


Lets begin with remoting in, click the connect to host shortcut and select one of your configued hosts, for this example we will use **niagara.scinet.utoronto.ca** but feel free to use any other host that you've confirmed works in the terminal.

When remoting in you will be shown two things, a dropdown menu to select the OS of your destination (pink arrow) -- select Linux. Along with a pop-up at the bottom informing you that the SSH is in progress (yellow arrow), **you must click on details every time as this will force VSCode to give you a spot to type in your passphrase, 2 factor authentication, and passwords to your SCC and Kimel**

![image](../_images/vscode/remoting_niagara.png)

Once you click details it should look like this, essentially a terminal window with popups above to type in your response, if there are no popups appearing simply click on the terminal window and type in your responses

![image](../_images/vscode/duo_ssh.png)

Once you've accepted your connection with duo, you should be prompted with a password(if you're not connecting to niagara/scinet), once inputted -- you've now successfully remoted in!

Now we'll cover how to navigate your way using Remote-SSH and share a workflow

##  Navigating

One of the coolest features about Remote-SSH is that you can emulate your directories, on VSCode *and* also have terminal instances avaiable for you to run bash commands or navigate your way around the host.

In the image below, the pink arrow is pointing to the file explorer tab, and the yellow arrow is pointing at the terminal instance button, click on and select what language (bash), note that you can open multiple instances at the same time.

Lets walkthrough the file exploer, click on the tab, and then click on open folder. Doing so, a pop up window will ask for the path to the directory you would to open in the file explorer window - the image below directs the path to my SCanD folder on the Niagara cluster. If you're ever lost in the terminal and want to know where you're are type in `pwd`. Once you're ready press OK. Depending on your system you *may* be asked to reauthenticate yourself

![image](../_images/vscode/niagara_files.png)

You should now be able to see your directories emulated on the sidebar, you can open and collapse things as you wish -- and also drag and drop directories/sub-directories, and files between each other as you would on a normal file explorer. Note tha you can also right click and download a file to your local machine if you wish - and also push local files too by dragging and dropping. 

Now that we can access our files and directories, we're now going to cover some **very** useful extensions that can help aide your workflow tremendously

![image](../_images/vscode/niagara_directories.png)


## Extensions

The heart of VSCode workflows are extensions, the extension marketplace is filled with very useful tools. We're going to suggest 3 fundamental extensions for your workflows that will aide in QC-ing, data wrangling, and analysis.

**Note: You will need to install your extensions for each host you remote into. This needs to be done only once**

### Live Preview

![image](../_images/vscode/live_preview.png)

Live preview by Microsoft is a powerful extension that allows users to emulate and preview their HTML files in real time, using all the files available given the drives are accessible to the system. This means, opening pipeline HTML files for QC-ing is now possible using VSCode. 

Simply right the HTML file in your directory and click on the "Show Preview" option, this will open and emulate the HTML file, as seen below. Note that you can also right click on the tab and open the HTML file on a new window to make parsing easier - should you require it.

![image](../_images/vscode/html_preview.png)

###  NiiVue

![image](../_images/vscode/niivue.png)

NiiVue is web-based visualization tool for neuroimaging that can open up DICOMS, NIFTIS, and a bunch of other medical imaging formats. This extension by no means replaces FSLEYES or FreeView, however it is a neat way to quickly view images for visual checks and also to see if things are aligned - which is useful during pipeline development. Moreover, you can open and overlay images on one another and view it all in 3D. As seen below, where a subjects aseg data(orange) was overlayed on to their T1w image(in grey) 

![image](../_images/vscode/niivue_overlay.png)


### Jupyter

![image](../_images/vscode/jupyter.png)

You can also install Jupyter to open up Python Notebooks which will be particularly useful for fMRI analysis, statistics work, and data wrangling. Note that you will also need to install the Python programming language and the Data Wrangler extension by Microsoft (this allows you to open up Pandas dataframes and run quick checks for distributions and missing-ness)

![image](../_images/vscode/python.png) ![image](../_images/vscode/data_wrangler.png)

Here is an example of a notebook in VSCode and viewing a Pandas Dataframe 

![image](../_images/vscode/jupyter_example.png) ![image](../_images/vscode/dw_example.png)



