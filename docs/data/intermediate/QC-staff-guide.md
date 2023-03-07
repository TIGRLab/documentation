### Who this is for
  - Kimel staff members troubleshooting QC/datman issues

## Missing Scans
There can be multiple causes for an expected scan (or entire session) to not show up in the dashboard, so it's helpful to start at the origin of the raw data and work your way forwards. If you're not familiar with which Datman scripts the study is using the first step should be to open `/archive/code/config/$STUDY_management.sh` as you debug. If the study you are working with doesn't use a script mentioned, you can rule out that step.

### [dm_sftp.py only] The zip file is not in `/archive/data/$STUDY/data/zips`
Make sure the zip file exists on the sftp server.
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


### [CAMH/ToNi scans only] "It's in the zips folder, but not in /archive/data/$STUDY/data/dicom"

### [CAMH/ToNi scans only] "It's in the dicom folder, but not on XNAT"

### "It's on XNAT, but hasn't been exported to /archive/data/$STUDY/data"

### "It's in the archive, but hasn't been added to the dashboard"

## Header Differences

## Misnamed Sessions

## Common Nightly Log Issues

## Missing REDCap

## Missing QC Metrics
