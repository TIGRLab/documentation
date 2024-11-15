### Who this is for
  - Kimel staff members troubleshooting QC/datman issues

-----------------

## Gold standards
We use gold standards to make sure the MRI scan parameters aren't changing over time. This section describes how to add gold standards to a study, or update existing ones.

- First, inside the study's metdata folder, make a 'standards' folder if one doesn't already exist.
- Next, for each scan site in the study, find one series per tag with correct header parameters. So if you have two scan sites (CMH1 and CMH2) and two tags (T1 and T2) you should find four gold standards (CMH1-T1, CMH1-T2, CMH2-T1, CMH2-T2).
- Copy the .json file for each series from the previous step into the `$STUDY/metadata/standards` folder.
- Switch to clevis (`sudo su clevis`), load the lab-code module (`module load lab-code`), and run `dm_update_standards.py $STUDY`. This will update the database with the newest gold standards.
- As long as there were no errors, you're good to go!

-----------------

## Header Differences
Some scans on the dashboard may have a 'header differences' warning label. This occurs when the scan's header values don't match the gold standard's header values. These warning labels shouldn't be ignored! Depending on the type and reason for differences there are several possible fixes, though.

### Removing 'Noise' from the Header Differences
- If the field being complained about is something we expect to change (e.g. AcquisitionTime) or a field that will only ever have meaningless changes (e.g. capitalization, punctuation changes) then you can completely ignore the field using [Datman's 'IgnoreHeaderFields' setting](http://imaging-genetics.camh.ca/datman/datman_conf.html#gold-standards).
- If the difference is just a very insignificant variation that we don't need to worry about, then you should use [Datman's 'HeaderFieldTolerance' setting](http://imaging-genetics.camh.ca/datman/datman_conf.html#gold-standards) to silence warnings unless a large (and meaningful) change pops up.

You can update these settings by adding to our defaults in the main config file (`/archive/code/config/tigrlab_config.yaml`) or you can add them in the study config file. If they're added to the study config file, the defaults from the main config file will still be included so you don't need to duplicate field names. Remember: These field names are case sensitive! Copy them exactly from the header differences.

Once the configuration values have been updated you have to update the database (see below) for the dashboard to remove these warnings.

```bash
# Make sure you're clevis
sudo su clevis
# load lab-code and all of dm_qc_report's dependencies
module load lab-code matlab/R2014a AFNI/2014.12.16 FSL/5.0.10
# This is safe to run as many times as you need. The --refresh flag will force a recheck of the
# headers and metadata without modifying files in the QC folder. Because of this it will
# finish almost instantly as well.
#
# Running on the whole study (e.g. dm_qc_report.py --refresh $STUDY) means each session is
# submitted to the queue.
#
# Running a single session (e.g. dm_qc_report.py --refresh $STUDY $SESSION) happens locally.
dm_qc_report.py --refresh $STUDY
```
### Removing 'Missing' Field Warnings
Dicom header fields that exist in the gold standard, but aren't found in the current scan's dicom headers, will have a 'Missing' warning.

- If the missing field is one we'll never care about you can silence the error by ignoring it as described in the previous section.

- If the 'missing' field exists under a different name in the scan, and it's a field we care about, then you should add a new gold standard (see Gold Standards section) so that we don't accidentally miss meaningful changes to scan parameters.

### Handling Real (or Potentially Real) Differences
First, if a difference exists and we expected that scan parameter to change (i.e. we were informed about a scanner change), then the solution is to update the gold standard (see Gold Standards section).

If we weren't notified, then the field difference must be dealt with on a case by case basis. Consult with Erin about whether we need to do anything/contact anyone or if we should just update the gold standards or ignore the field etc.

-----------------

## Missing Scans
There can be multiple causes for an expected scan (or entire session) to not show up in the dashboard, so it's helpful to start at the origin of the raw data and work your way forwards. If you're not familiar with which Datman scripts the study is using the first step should be to open `/archive/code/config/$STUDY_management.sh` as you debug. If the study you are working with doesn't use a script mentioned, you can skip that step.

### [dm_sftp.py] The zip file is not in `/archive/data/$STUDY/data/zips`
First, make sure the zip file exists on the sftp server. Obviously, if the study uploads directly to xnat, you can skip this entire section :)

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

### [dm_xnat_extract.py] The scan is on XNAT, but not in the nii and/or bids folder
Section under construction, nag me about it if you came here looking for help! - Dawn

-----------------

## Missing REDCap
#### 1. Make sure the survey exists on the server.

#### 2. Make sure the MRI ID field is correct.

Often a small typo in this field, or the omission of one of the ID subfields, can prevent the REDCap record from being correctly matched to our archive. If you want to be certain that the ID format is correct you can use datman's `scanid.parse` module. This is how all of our nightly scripts and the dashboard read these IDs, so if the ID can be read without raising an exception you can be certain that it is formatted correctly.

```bash
# First load our code environment and start ipython
module load lab-code
ipython
```
```python
# Do these lines in ipython
import datman.scanid
ident = datman.scanid.parse("YOUR_ID_HERE") # If this line doesnt raise an error, the ID is well formatted.
```

#### 3. (Data Entry Trigger only) Rule out temporary network issues.

Studies that use REDCap's data entry trigger feature to import records may occasionally end up with missing records if there happened to be network issues at the time that the survey was completed. To check if this is the case, re-save the record and then refresh the page in the dashboard. If it appears then it was a temporary issue that requires no further intervention.

#### 4. (dm_redcap_scan_completed.py only) Re-run the script manually.

Studies that use the datman script `dm_redcap_scan_completed.py` instead of (or in addition to) the Data Entry Trigger will only pull in records when it runs as part of the nightly pipelines. If the record was entered on REDCap on the same day you're looking for it there may not actually be an issue, and you can run the script manually to pull it down early.

If at least one day has passed, you can likely find error messages in the nightly run log for this script. To help with debugging and get more debug info you may want to manually run the script.

```bash
module load lab-code
# For more log messages use --verbose or --debug
dm_redcap_scan_completed.py STUDYNAME
```

#### 5. Make sure REDCap configuration for the study is correct.
**DET only:** Make sure the data entry trigger is enabled and pointing to the correct server. Navigate to the project, and then select the 'Project Setup' tab. Scroll down to the 'Additional Customizations' button. When you click the button, somewhere in the list of features you should find a checkbox for 'Data Entry Trigger'. Make sure it's checked, and make sure the URL is `http://172.26.216.66/redcap`. If everything is configured right, you can click the test button to make sure redcap can reach the server. If it reports an error, the network configuration may have changed and you should contact Dawn for help! *Note:* The data entry trigger may only be configured for redcap records on CAMH's server.

**Both:** The REDCap configuration for each study can be found in the study config file (e.g. `/archive/code/config/MYE01_settings.yml`). To learn more about each field see the [configuration documentation here](http://imaging-genetics.camh.ca/datman/datman_conf.html#redcap)

Make sure that the values in the settings file match the actual redcap form field names.

**DET only:** pay special attention to the RedcapUrl, RedcapProjectId, RedcapInstrument and RedcapRecordKey as these are what the server sends with the initial trigger. To see what's really being sent and compare it to what you expect you can log into the dashboard's server and check the log for exceptions while re-saving the record.

```bash
ssh srv-dashboard.camhres.ca
# This will update with newest log messages. Leave this process running while
# you re-save the record on REDCap and then return to look for error messages.
sudo tail -f /var/log/uwsgi/app/dashboard.log
```

If one of these settings fields are misconfigured you should see a message that looks like this
```
[dashboard.blueprints.redcap.utils] INFO - Failed to find redcap config for record YOUR_RECORD_ID_HERE in project YOUR_PROJECT_NUM_HERE on server YOUR_REDCAP_URL_HERE with instrument YOUR_INSTRUMENT_HERE. Exception - Can't locate Redcap config.
```

You can then update the settings file to match the values from the log message if they differ from what was expected. After updating the settings file you should run parse_config.py to push the changes to the dashboard database.

```bash
module load lab-code
parse_config.py STUDYNAME
```

Once this is finished, you should be able to re-save the survey again (or re-run dm_redcap_scan_completed), refresh the dashboard page and the survey should appear.

If it's still missing at this point, contact Dawn asap!

-----------------

## Common Nightly Log Issues
Section under construction, nag me about it if you came here looking for help! - Dawn

-----------------

## Missing Metrics
Section under construction, nag me about it if you came here looking for help! - Dawn

<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| TIGRLab | April 24th, 2023 | Did annual review together. Looks fine. |
| Dawn | October 7th, 2024 | Reviewed and updated some of contents. |
| Dawn | November 14th, 2024 | Added content to 'Header Differences' section. |
<!-- sign-off-sheet:end -->