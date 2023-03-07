### Who this is for
   - Anyone doing quality control for the lab

The QC Dashboard was developed in the Kimel Lab and released in December 2016 in an effort to better conduct MRI QC. It provides a unified interface for examining individual participant data and communicating issues with others.

---

## Logging in

The Dashboard is found at http://srv-dashboard.camhres.ca/. This website is only
available on CAMH's network, which means that if you're working offsite you must first log in to https://remote.camh.ca and open Google Chrome through that site before entering the dashboard's URL.

The Dashboard authenticates users via GitHub, so if you do not have a GitHub account you will need to [make one](https://github.com). The first time you access the dashboard you should click on the request account button, as shown below. This will take you to a page where you can request access for your GitHub account. Once your account has been approved by an admin, you can return to http://srv-dashboard.camhres.ca and use the 'GitHub Login' button to login with your github username and password. If you do not receive approval from an admin within 72 hours email us at tigr.lab@camh.ca to ask us for assistance.

![](../../_images/qc_dash/account_request.png)

---

## Dashboard Basics

### Study View

After logging in you should see a list of studies you have access to. Click on
one to see the study details. If this list is missing a study you need access to contact us at tigr.lab@camh.ca or on slack.

![](../../_images/qc_dash/main_view.png)

First, on the study home page you may see a box outlining any outstanding QC for the study.

![](../../_images/qc_dash/outstanding_qc.png)

* **Missing Scans** - A REDCap form has been filled out for the ID, but no scan data exists in the archive yet. This means either it has not been uploaded to XNAT or there was an error in transfer from XNAT.
  * If the error has been there for _48 hours or more_ it must be investigated.
  * [See here for details on how to fix it](#missing-scans)

* **Missing REDCap** - Scan data has been received for the ID but a REDCap scan completed form has not been filled out or has an incorrect subject ID. If this error has not disappeared within _48 hours_ of receiving the data it must be investigated.
  * If we have access to the REDCap form: login and search for any forms that may have been misnamed. If one is found, fix the subject ID field and save the form again. The error should disappear when you refresh the dashboard. If none exist, contact the RA who was responsible for entering the form.
  * If we do not have access to the REDCap form: Contact the RA who is meant to enter the data and ask them to investigate.

Next, you may see a 'Nightly Run Log' box showing the most recent run of the nightly pipelines for this study. Error messages in this run log should be resolved as soon as possible, when found. If you're not a Kimel Lab staff member you should contact one promptly to draw attention to any errors. If you are, [see here](#common-nightly-log-issues) for more info on resolving common issues.

![](../../_images/qc_dash/nightly_run.png)

Most QC will be done from the 'timepoint' page. Select a session by either clicking on a row from the 'Outstanding QC' window, or by clicking 'Session list' and then clicking on a session ID.

![](../../_images/qc_dash/session_list.png)

### Timepoint view

- Delete all: Remove everything associated with this timepoint from the archive and/or the dashboard database. Original raw data from the scanner will never be deleted so this is fairly safe to use. The most that stands to be lost permanently is records of already completed QC and user comments. This button is mainly useful for removing sessions that have slipped through with a bad subject ID. [See here for more info.](#misnamed-scans)
- Flag Incidental Finding: Notify staff members about an incidental finding.
- Create issue: Open a GitHub issue to notify staff members about missing data or other QC related issues.
- Comments: Used to hold notes during the QC process or make a record of anything unusual. No notification is sent to staff, it's only for personal record keeping.

![](../../_images/qc_dash/timepoint_view.png)

- Delete session (trash can icon): Delete a session from the archive and/or dashboard database. This will not delete user comments or other sessions associated with the timepoint.
- KCNI ID: Session named based on KCNI naming convention (used on XNAT by our lab).
- Task Files: Files related to functional scans where tasks are recorded.
- REDCap Comment: RA comments about the in scanner behavior of the participant.
- View Survey: Takes you to the REDCap form filled out for the session.
- View Tech Notes (CAMH scans only): the notes from the MRI technician at the time of the scan (Check before MRI Scans).

![](../../_images/qc_dash/session_view.png)

- Series: Scan Number
- Tag: Type of Scan
- Length: Corresponds to time or frames, these values are consistent within each project
  - Functional Scans: Typically longer lengths
  - Anatomical scans: No length applicable
  - DTI: often ~103-104
- Description: The series description taken from XNAT. Click on this to access the scan view.
- Status: New, signed off, flagged or blacklisted. Signed off == Everything looks good. Flagged == Something is unusual but the data can probably still be used. Blacklisted == Scan is unusable. Blacklisting indicates an artifact is drastically affecting the scan.
- Warnings: Indicates scan issues like repeated scan or header mismatch
- Comment: Displays the comment entered when a scan is blacklisted or flagged
- Delete (trash can icon): Appears on hover. Remove only a particular scan from the archive and/or dashboard database. All previously completed QC for other scans will be left intact, as well as user comments.

![](../../_images/qc_dash/scan_table.png)

- Reached by clicking the series description of a scan in the scan table
- Header differences will display here (if any exist)
- The papaya viewer allows you to scroll through the scan to look for artifacts

![](../../_images/qc_dash/scan_view.png)

---

## QC Process Overview
Before signing off on a session you should make sure that none of the issues listed below exist, or that they have been resolved if they do. For detailed information on how to use the dashboard itself, [see here](#dashboard-basics)

<table>
  <thead>
    <tr>
      <td rowspan="2" align="center">Problem</td>
      <td colspan="2" align="center">Solution</td>
    </tr>
    <tr>
      <td align="center">Kimel Staff</td>
      <td align="center">Other Users</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>An expected scan is missing from the session.</td>
      <td><a href="#/data/intermediate/QC?id=missing-scans">See here.</a></td>
      <td>Unless the scan has tech notes and the technician left a note that those scans were not completed, contact the appropriate staff member. Don't sign off on the session until all scans are located or confirmed to not exist.</td>
    </tr>
    <tr>
      <td>The session has a 'missing redcap' message.</td>
      <td><a href="#/data/intermediate/QC?id=missing-redcap">See here.</a></td>
      <td>Contact the appropriate staff member. Do not sign off on the session until the REDCap survey has been found or is confirmed to not exist.</td>
    </tr>
    <tr>
      <td>(CAMH scans only) The tech notes contain a message from the MR Tech.</td>
      <td colspan="2" align="center">Blacklist any scans if instructed to do so in the notes. Copy any tech note messages into the blacklist comment.</td>
    </tr>
    <tr>
      <td>There is an error message referencing the session ID (or one of the session's scans) in the most recent nightly log</td>
      <td><a href="#/data/intermediate/QC?id=common-nightly-log-issues">See here.</a></td>
      <td>Contact the appropriate staff member. Don't sign off on the session until all associated errors are resolved.</td>
    </tr>
    <tr>
      <td>A scan from the session has a geometric pattern or an unfamiliar artifact</td>
      <td>Bring it to the group to determine next steps / if the data can be salvaged. Ensure the appropriate MRI physicist is contacted.</td>
      <td>If it's a new artifact, or you're unsure if the artifact is too severe for the data to be used, contact the appropriate kimel staff member. Otherwise, flag the scan with a descriptive message.</td>
    </tr>
    <tr>
      <td>A scan cuts off part of the brain.</td>
      <td colspan="2" align="center">Flag the scan with a descriptive message.</td>
    </tr>
    <tr>
      <td>A scan has header differences listed.</td>
      <td><a href="#/data/intermediate/QC?id=header-differences">See here.</a></td>
      <td>Contact the appropriate staff member. Don't sign off on the scan or the session it belongs to until the issue has been resolved.</td>
    </tr>
    <tr>
      <td>A series is truncated (i.e. its 'length' in the scan table is shorter than expected for that type of scan).</td>
      <td colspan="2" align="center">Blacklist the truncated series.</td>
    </tr>
    <tr>
      <td>The session has repeated series.</td>
      <td colspan="2" align="center">If only one of the repeated scans is usable, approve it and blacklist the others. If all of them are bad/mediocre instead flag all of them with a descriptive message so that pipelines can average the files.</td>
    </tr>
    <tr>
      <td>There's an incidental finding (e.g. a tumor or other medical anomaly)</td>
      <td>Bring it to the group immediately to determine next steps. Contact Arash Nazeri for further information on the issue. Once the issue has been resolved, use the 'incidental finding' button in the dashboard to record relevant info.</td>
      <td>Contact the appropriate staff member immediately.</td>
    </tr>
    <tr>
      <td>The distortion maps are not distorted in opposite directions (i.e. they appear identical)</td>
      <td>Open a GitHub issue to document the problem and contact the appropriate MRI physicist.</td>
      <td>Contact the appropriate staff member.</td>
    </tr>
  </tbody>
</table>

---

## Visual Inspection of Scans

This section is an overview of things to look for when QCing the raw data (i.e. everything on the main timepoint view of the dashboard). For pipeline-specific QC [see this page instead.](../../resources/Pipeline-QC-guide.md)

After viewing the tech notes (if they're available), you should scroll through the images on the main session page. Below is a list of things to look out for different types of scans (aside from that all scans should resemble a human brain).

T1 and T2 scans:
  - T1 and T2 should be inverted in color

Resting state scans:
  - Look for artifacts in the SNFR scan
  - It's expected to see artifacts related to movement
  - If artifacts are unusual (unexpected/new), that’s where it should be made known to the MR physicist

DPA and DAP (or DTI)
  - Check that the scans vary from each other (distorted in opposite directions)
  - Check that they resemble a brain

3D Plot (BVEC)
  - Should resemble a ball to show the different directions

- Truncated scans should be blacklisted to keep out of BIDs format

### Examples of Common Artifacts

#### Motion Artifact (Ringing):
Can see rings near the posterior end of the brain.

![](../../_images/qc_dash/artifacts_ringing.png)

### Motion Artifact (Shadowed Arc Artifact):

Image is tilted and can see long lines following the outline of the skull.

![](../../_images/qc_dash/artifacts_arc.png)

### Ghosting:

Can see lighter instances of scan outside of main Field of View.

![](../../_images/qc_dash/artifacts_ghost.png)

## Common Issues
This section is for Kimel Staff members to use to help resolve common issues with our nightly pipelines / the dashboard. Others are welcome to read it if they are curious about how things work, but you likely will not have permissions to implement any of the mentioned fixes!

### Missing Scans
There can be multiple causes for an expected scan (or entire session) to not show up in the dashboard, so it's helpful to start at the origin of the raw data and work your way forwards. If you're not familiar with which Datman scripts the study is using the first step should be to open `/archive/code/config/$STUDY_management.sh` as you debug. If the study you are working with doesn't use a script mentioned, you can rule out that step.

#### [dm_sftp.py only] The zip file is not in `/archive/data/$STUDY/data/zips`
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


#### [CAMH/ToNi scans only] "It's in the zips folder, but not in /archive/data/$STUDY/data/dicom"

#### [CAMH/ToNi scans only] "It's in the dicom folder, but not on XNAT"

#### "It's on XNAT, but hasn't been exported to /archive/data/$STUDY/data"

#### "It's in the archive, but hasn't been added to the dashboard"

### Header Differences

### Misnamed Scans

### Common Nightly Log Issues

### Missing REDCap
