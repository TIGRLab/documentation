### Who this is for
  - Kimel staff members troubleshooting QC/datman issues

## Missing Scans
There can be multiple causes for an expected scan (or entire session) to not show up in the dashboard, so it's helpful to start at the origin of the raw data and work your way forwards. If you're not familiar with which Datman scripts the study is using the first step should be to open `/archive/code/config/$STUDY_management.sh` as you debug. If the study you are working with doesn't use a script mentioned, you can skip that step.

### [dm_sftp.py] The zip file is not in `/archive/data/$STUDY/data/zips`
First, make sure the zip file exists on the sftp server.

- Find the value of `FtpServer`, `MrUser`, and `MrFolder` for the study (and optionally, `FtpPort` if the study or site defines it). These may be defined in the main config file for datman `/archive/code/config/tigrlab_config.yaml` or in the study config file in that same folder. The study config overrides the main one, and individual sites override general study configuration, so if a value has been defined multiple times take the 'most specific' value relevant to the session you're looking for.
- Get the password. By default this will be in a file in `/archive/data/$STUDY/metadata/` named `mrftppass.txt`. But if a study/site defines the value `MrFtpPass` look for a file matching that non-default name instead.
- Connect to the sftp server.
    ```bash
    sftp ${FTP_USER}@${FTP_SERVER}

    # If the study defines FtpPort use this instead
    sftp -P ${FTP_PORT} ${FTP_USER}@${FTP_SERVER}
    ```

- Navigate to the folder matching `MrFolder` and look for the scan. You can use normal shell commands like `cd` and `ls`.

If the zip file is not in the expected folder (`MrFolder`) check any other folders the sftp user has access to. Occasionally scans might start being put in a new folder without us being notified.

- If you find it in a new folder, update the study config file's `MrFolder` value. This setting can accept a list of folder names or you can give it a regular expression. For example, for the study TAY you could set `MrFolder: [TAYS1MR, TAYS2MR]` or `MrFolder: TAYS*1MR`. The latter would be preferable if it's possible there may be future TAYS#MR style folders and the former is preferable if there may be similarly named folders that should not be pulled from.

If the scan does not exist anywhere on the server and it has been more than 48 hours, contact the scan site to see why it has not been uploaded to the MR server.

If it turns out the scan is exactly where it's expected to be, try manually running dm_sftp with verbose logging to identify errors. If the scan still does not appear in `/archive/data/$STUDY/data/zips` when the script completes you may have a more serious/complicated issue that should be kicked up the chain for debugging.

  ```bash
  # You should do this from tigrsrv and you should be user clevis
  sudo su clevis
  module load lab-code
  dm_sftp.py --verbose $STUDY
  ```

### [dm_link.py] The scan is in the zips folder, but not in `/archive/data/$STUDY/data/dicom`

This is likely a result of dm_link.py not being able to assign a valid ID to the session. If this is the case, you'll see an error message in the nightly log like this one: `dm_link.py - $STUDY - ERROR - Scanid not found for archive: ${the zip file you're investigating}` or a warning like this one `dm_link.py - $STUDY - WARNING - ${the zip file you're investigating}: ${PatientName} (header PatientName) not valid scan ID`

To fix this, you must manually assign the correct ID in `/archive/data/$STUDY/metadata/scans.csv`. Enter the zip file's name (minus the file extension) in the first column, the correct session ID for this scan in the second column, the value of the PatientName field from the dicom headers in the third field (get this from the warning message or the dicom headers) and the StudyID in the final column (get this from the 'Ex#####' part of the zip file name or the dicom headers).

To check that you've updated it correctly, you should run dm_link.py to see if the error disappears.

```bash
# Should run this as user clevis
sudo su clevis
module load lab-code
dm_link.py $STUDY $PATH_TO_ZIPFILE
```

### [dm_xnat_upload.py] The scan is in the dicom folder, but not on XNAT
Sometimes this happens because a scan gets stuck in XNAT's pre-archive so first [check here.](https://xnat.camh.ca/xnat/app/template/XDATScreen_prearchives.vm)
If the session ID appears in this list select it, click details (on the right), and if all the data appears ok click the archive button.

One thing to be careful of is that sometimes the scan got stuck there because there are multiple UIDs present in the same zip file. Sometimes this happens with the processed images / 3d saved state processed images. To tell if that happened here check to see if the session ID is in the pre-archive twice with the same upload date. If you click on each they should contain a different list of series (one will have most of the scans and the other just a few processed scans). If this is the case archive the main scan data first and then archive the smaller set of data and dismiss the error about the different UIDs. If you fail to merge in both dm_xnat_upload.py will continue to try to upload the zip file every night due to the missing files.

###TO DO
- Add overview of how to handle header differences
- Add overview of how to handle missing scans due to incorrect ExportInfo / dicom2bids.json settings
