## Who this is for:
  - Lab members who need to set up a new study in the archive
  - Those who want to learn more about how we set up our studies

## Setup process
This page describes the current process for correctly setting up a new study to be managed by [Datman](https://github.com/TIGRLab/datman) and the [QC dashboard](https://github.com/TIGRLab/datman-dashboard). Be sure to follow all steps carefully, missing a step can cause many issues that will take more time to fix later.

**NOTE:** Everything in the archive should be owned by user clevis and the group kimel_data! Don't run commands in the archive as yourself or as root, please, it can break many things.

### 1. Switch to user clevis.
Clevis is the user account we use to manage the archive and run our automated pipelines.
  ```bash
    sudo su clevis
  ```

### 2. Make the project folder tree.
The below commands will create the correct directory structure with correct permissions and ownership. Everything in the archive should be owned by user clevis and group kimel_data.
   ```bash
     # Set this to the new study's name.
     STUDY=YOURPROJECT

     # Make project folder and subfolders
     mkdir -p /archive/data/${STUDY}/{bin,metadata,data,docs,pipelines,qc}

     # Make sure everything is owned by user clevis and group kimel_data
     chown -R clevis:kimel_data /archive/data/${STUDY}

     # Set correct permissions on all folders
     chmod 2775 -R /archive/data/${STUDY}
   ```

### 3. Create a README for the project.
Each project should have a README.md file with details about the study (e.g. `/archive/data/$STUDY/README.md`). This file should contain a brief description of the dataset, who the PI is, contact info for RAs and any other important info that you'd want to convey to anyone using the dataset. This file is the same one that can be viewed and edited from inside the QC dashboard's study home page.

### 4. Fill in the docs folder.
In each study's docs directory (e.g. `/archive/data/${STUDY}/docs`) you should add important documentation, where available. For example:

  - Make a `protocols` sub-directory. Get a 'protocol' file from each scan site and store all files here.

  - If you have contact info for other scan sites make a `contacts` folder. Add a file for each site, following a `contacts-$SITE-$DATE` format. The date should always reflect the most recent update of the info.

  - If you have any REB documentation for any scan sites, make a `REB_docs` folder here. Make a subfolder named after each site for the site's documents and place the files there.

  - If you have any files documenting standard operating procedure for different sites, make a subfolder named `SOPs` and place these files there.

### 5. Set up XNAT for the project.
The most up to date info about how to get an XNAT account or request a new XNAT project is available on the landing page of the [XNAT server here](https://xnat.camh.ca/), when you're not logged in.

Note: The XNAT project, after it's made, must allow the `tigrlab` XNAT user read/write access for us to manage the data. If you don't have the ability to add a user to the project, contact Dawn with the XNAT project ID and she can add configure it.

### 6. Set up a REDCap scan completed survey
This usually only applies to studies that collect data at CAMH (though some external sites may have their own redcap servers and can take advantage too).

Many of our studies have a redcap survey that gets filled in by the RA at data collection time. It details which series were successfully collected and if any issues happened during data collection. We can pull these in automatically to get advance notice to look out for data for a subject or easily recognize if something went wrong during scan data upload.

The easiest way to 'turn on' data collection for studies collected at CAMH is to modify the existing 'scan completed' survey:
  - Go to https://edc.camhx.ca/redcap/

  - Log in as the tigrlab user (info in our passpack).

  - Access the 'Scan Completed' survey and add the new study to the options for study and add any research assistants to the list. If you don't have REDCap access to do this yet ask another staff member.

If a new survey is used (or a different server), just make sure to get an API read-only token for the tigrlab user and reach out to Dawn with the details so she can set up the automated data retrieval.

### 7. Add required files to the metadata folder.
- Create the 'scans.csv' file to hold name mappings when the dicom files don't contain a valid ID.
  - Add a file named `${STUDY}_scans.csv` in `/archive/code/scan_csvs/`
    - Add `source_name     target_name     PatientName     StudyID` to the file and save it.

    - Commit the new file.
        ```bash
          # Change to user clevis
          sudo su clevis
          # Commit the file
          git add ${STUDY}_scans.csv
          git commit -m "Added initial scans.csv for study ${STUDY}"
        ```

    - Make a symlink named `scans.csv` in the study's metadata folder that points to this file.
       ```bash
         # Change to the study metadata folder
         cd /archive/data/${STUDY}/metadata
         # Make a relative symlink to the file you just made
         ln -s ../../../code/scan_csvs/${STUDY}_scans.csv ./scans.csv
       ```

- If you have any study design related files (e.g. event timing files), add a folder called `design` and save them there for later reference.

- (Only if REDCAP scan completed/ID sharing used) Add the redcap token in a file named `redcap-token`. If the study has more than one redcap server to access (e.g. other sites have their own external server) each additional token can be added in its own file under whatever name you like.

- (Only if SFTP server used) Add the FTP server password in a file named `mrftppass.txt`. If the study has more than one sftp server to access (e.g. if other sites have their own external sftp) each additional password can be added in its own file under whatever name you like.

- (Only if using multiple XNAT servers) By default the xnat credentials are retrieved from environment variables. However, if a study has scan sites that push/pull from other xnat servers you can create a file here with any name you like, with the username on the first line and the password on the second line.

- Set correct permissions for credential files. The redcap token, sftp password, and xnat credential file(s) should only be readable by user clevis.

```bash
  # Make sure the files are owned by clevis. Make sure to run this on any additional credential files if you have more.
  sudo chown clevis /archive/data/${STUDY}/metadata/{redcap-token,mrftppass.txt,xnat-credentials}
  # Fix permissions
  chmod 600 /archive/data/${STUDY}/metadata/{redcap-token,mrftppass.txt,xnat-credentials}
```

### 8. Create the dcm2bids configuration file.
To generate bids format outputs you'll need to create a dcm2bids configuration file. [See this page](https://github.com/TIGRLab/admin/wiki/Exporting-to-BIDs) for more info. As per that page, you should ideally set up XNAT to handle the output generation, unless you have a specific reason to do it locally, so do that also.

### 9. Create the Datman study config file.
- You can refer to the [Datman documentation here](http://imaging-genetics.camh.ca/datman/datman_conf.html) for more info on this file and available settings. You can also consult our existing config files in `/archive/code/config`, all of which follow a `${STUDY}_settings.yml` naming convention, for examples. Our main config file is also in this folder and named `tigrlab_config.yaml`

- There's a template settings file you can copy at `/archive/code/datman/assets/config_templates/study_config.yml`, or you can copy and modify the settings file from another study likely to be similar to yours.

- To fill in this settings file you need:

  - A list of expected scan types for each site in the study.

  - Knowledge of what will be in the actual SeriesDescription fields for the dicoms received. That is, you will probably need at least one scan to have been completed already. You can view the series descriptions on XNAT but you can also easily get info from your dicom headers with 'dcmdump' (its installed on all our workstations). For example:
    ```bash
        # Get the series description from a dicom in a series
        dcmdump --search SeriesDescription $PATH_TO_A_DICOM
    ```
  If you omit the search option it will show you the whole header. Note that the search option is case sensitive (i.e. 'SeriesDescription' works but 'seriesdescription' does not).

   - Add the new study's project settings file to the `/archive/code/config` directory with the others. Ensure it follows our naming convention (``$STUDY_settings.yml``).

### 10. Add your new study's settings file to the Datman main config file.
- Inside `/archive/code/config/tigrlab_config.yaml`, in the 'Projects' section, add your study and its settings file name to the list or Datman won't recognize it.

- If you created any brand new scan tags (i.e. a tag that has never been used by any study before) add an entry for each new tag to the 'ExportSettings' section in `tigrlab_config.yaml`

- Check that your file is being found and is free of syntax errors by loading the config in python:
    ```bash
      # These commands must be done in the terminal
      module load lab-code
      ipython
    ```
    ```python
      # These commands are done in ipython after you've run the above commands.
      # Import Datman's config manager package
      import datman.config
      # Attempt to load your new study's configuration into Datman
      config = datman.config.config(study="YOURSTUDYNAMEHERE")
    ```

  If the last command ran without errors, then your new study's configuration is accessible to Datman and free of syntax errors!

### 11. Set up the study's nightly run script.
- Add a script named `${STUDY}_management.sh` to `/archive/code/config/` to define all the Datman steps (and other scripts) that will run on the data. [See here](http://imaging-genetics.camh.ca/datman/script_overview.html) for more info on what each script does and for more information on configuration requirements. Copy another study's script to make your life easier :)

- Most of the folders in the study's `data` folder get populated by `dm_xnat_extract.py` and the `qc` folder gets populated by `dm_qc_report.py`. These scripts will also populate the dashboard database. The `pipelines` folder is typically populated by non-datman scripts that are manually run ad hoc or on a quarterly basis.

- Make sure the management script is executable. `chmod 754 /archive/code/config/${STUDY}_management.sh`

- Make a link for the script in the study's bin folder. This link should be named `run_data_kimel.sh`
  ```bash
    cd /archive/data/${STUDY}/bin
    # Make it a relative link so it doesn't break if run from the SCC!
    ln -s ../../../code/config/${STUDY}_management.sh run_data_kimel.sh
  ```

- Turn the script on in `/archive/code/bin/run.sh`. This is the launcher script that runs every active study's pipeline nightly. To turn on a new study just add it to the list of projects near the beginning.

- Note that steps that interact with XNAT have been separated out, because the server had been causing enormous delay to the nightly pipelines. Calls to ``dm_xnat_upload.py`` now go in ``/archive/code/bin/run_upload.sh`` and calls to ``dm_xnat_extract.py`` go in ``/archive/code/bin/run_extract.sh`` instead of being listed in each study's management script. In both instances, now, you just add the study ID to the appropriate study list in the file or add a new section if you need to run with different settings.

### 12. Commit your configuration changes and push them to GitHub.
  ```bash
    # This should be done as clevis!
    git add ${STUDY}_settings.yml ${STUDY}_management.sh tigrlab_config.yaml
    git commit -m "Adding settings for ${STUDY}"
  ```

### 13. Add the new study's configuration to the QC dashboard.
The QC dashboard needs to re-read the settings whenever changes are made to metadata or tags. It will create the study in the database and populate all the necessary details if you run the below code:

```bash
  # Make sure you're clevis when you run this. i.e. `sudo su clevis` first

  # Load our module
  module load lab-code

  # If you've added any new tags to tigrlab_config.yaml, you should run parse_config.py
  # without specifying a study to ensure the global configuration settings update too.
  parse_config.py

  # Otherwise, you can run it just for the new study.
  parse_config.py ${STUDY}
```

### 14. Give all the necessary RAs access to the project on the dashboard.
- Get each RA to make a GitHub account if they don't already have one.

- Once they have a GitHub account they should go to srv-dashboard.camhres.ca and request an account

- Once they've made the request, their access has to be approved by a dashboard admin (every lab employee should be an admin). Just approve their request on the 'admin' page and 'add' any studies (or study-sites) they should have access to, to their account. Be sure to give them the correct access (i.e. people doing QC should be marked as such on the admin management page)

- Also, invite their github account to our organization, so they can add and view github issues and our private documentation if needed.

### 15. Arrange a QC training session for RAs who are new to doing scan QC.
If they've never done QC before we should meet with them to ensure they don't accidentally miss important errors that must be corrected. Contact Erin for more info about who should train them and how.

### 16. Set up QC gold standards
We use gold standards (basically json sidecars for scans that match the protocol for each series and scan site) to automated checks to catch when scanner settings may be changing in ways we don't want. To set them up, [see here](https://imaging-genetics.camh.ca/documentation/#/data/QC-staff-guide?id=gold-standards)

### 17. Finally, wait a day or two and make sure all looks good.
A few days after you've finished setting up all the nightly run scripts, gold standards, etc. Check back in and make sure it looks like everything is extracting where it's supposed to, with the tags you expect, the dashboard is populating, and no major errors are cropping up in the logs. If all looks well, then you're done!

<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| Dawn | September 25, 2026 | Massively updated contents. Reflects what we actually do now. |
<!-- sign-off-sheet:end -->
