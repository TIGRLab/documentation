## MR Database Information
Neuroimaging data collected during the study will be stored in an **online MRI Database (XNAT) server** hosted by the Centre for Addiction and Mental Health (CAMH)’s **Krembil Centre for Neuroinformatics (KCNI)**.

**URL:** https://xnat.camh.ca

**KCNI Documentation:** https://kcniconfluence.camh.ca/display/NPP/

Contact information for issues etc. and other documentation can be found on the home page before logging in.

## Accessing the MRI Database
For details on how to:
  - Get login access for a new account
  - Get access to an existing project
  - Make a new a new project

Consult the instructions on the [landing page of XNAT](https://xnat.camh.ca).

## XNAT Naming Convention
In order to facilitate automatic data management, imaging data uploaded to XNAT must adhere to a common naming convention as implemented by the Krembil Neuroinformatics Institute (KCNI) Platform at CAMH. Further information can be found in the following links:
- [XNAT Naming Convention](https://kcniconfluence.camh.ca/display/NPP/XNAT+Naming+Convention)

Data stored in XNAT adheres to the following hierarchy:
- Project
  - Subject A
    - Experiment A
      - Scan 1
      - Scan 2
      - Scan 3
    - Experiment B
      - Scan 1
      - Scan 2
      - Scan 3
  - Subject B
    - Experiment A
      - Scan 1
      - Scan 2
      - ...

## XNAT Data Upload Procedure

XNAT data upload must occur **as soon as possible** after the MR scanning session. Scans acquired at CAMH can be automatically uploaded to XNAT. Contact the XNAT server admin (or Dawn) to set this up for new projects.

### Uploading DICOM Data

As much as is possible, data should be submitted to XNAT as a single .zip file containing all DICOM data from a single ‘session’ in the scanner. To upload DICOM data, use [this guide](https://kcniconfluence.camh.ca/display/NPP/MR+Upload+Instructions).

If using the Compressed Image Upload tool, please be patient. Transfers can take ~30 minutes or more for a complete dataset. We do not need demographic information (e.g. age, sex) for which XNAT displays data entry fields. You may enter this (non-identifying) information if you like, but it is not recommended nor analyzed.


### Uploading non-DICOM Data

**IMPORTANT:** NON-DICOM DATA CANNOT BE RECEIVED IN THE SINGLE .ZIP FILE. BEHAVIOURAL MEASURES, MRI TECH NOTES IN PDF FORM, AND SOME SITES’ PHYSIOLOGICAL SIGNAL FILES NEED TO BE UPLOADED SEPARATELY.

Please create a separate file for these types of data in the participant’s ‘resources’ folder, in accordance with [these general instructions](https://kcniconfluence.camh.ca/display/NPP/Non-DICOM+Upload+Instructions).

Any task data output (both .txt and .edat2 files) should be uploaded to a folder under ‘resources’ called ‘behav’. In the unusual situation that any NIfTI files are to be uploaded, they should be uploaded to a folder under ‘resources’ called ‘NII’.

<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| TIGRLab | April 24th, 2023 | Did annual review together. Looks fine. |
| Dawn | September 24th, 2026 | Made some minor updates, removed broken links. |
<!-- sign-off-sheet:end -->
