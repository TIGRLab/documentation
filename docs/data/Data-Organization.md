## Who this is for
   - Anyone who has to work with the data stored in `/archive`
   - Anyone interested in how we manage the data in `/archive`

## Overview
The data on the TIGRlab system lives in `/archive/data` (this is identical to `/archive/data/data-2.0`).

Briefly, MRI data is uploaded to our [XNAT database](https://xnat.camh.ca/xnat/), downloaded into `/archive/data` by [`datman`](https://github.com/TIGRLab/datman), and then further inspected and pre-processed by various pipelines. All important metadata is also stored here regarding data quality and organization.

The folder structure for a typical project will look like this:
```
/archive/data/STUDYID
    bin/
    data/
    docs/
    logs/
    metadata/
    qc/
    pipelines/
    README.md
```

**bin/**

Contains any scripts that we run on the project. Every project has a `run_data_kimel.sh` script in this folder that we run nightly. If you have any questions about what processing is happening on a project check this file first. Some older projects also have a `run_pipelines_scc.sh` file in this directory which contains commands we _used_ to run on the data using the SCC. We no longer run any nightly processing on the SCC so these are retained mostly for record keeping.

**data/**

The organization of the `data/` folder is roughly:

```
filetype/
    subject1/
        file1.<ext>
        file2.<ext>
    subject2/
        file1.<ext>
        file2.<ext>
```

The `filetype/` folder name tells you a little about what kind of data you should expect to find within, organized by each subject. Here are the most common ones for raw data:

+ `zips/`: in studies collected at our scanner (`CMH`), or UofT's scanner, the raw `.zip` files that come off of the MR server are stored here. Recently, even CMH scans are being uploaded directly to XNAT so this folder may not exist for newer studies even if the study is collecting at CAMH.
+ `dicom/`: Contains symlinks to the zip files in `zips/`, but with our naming convention applied. Some of these renames are defined manually by the `metadata/scans.csv` file, described later.
+ `nii/`: DICOM-to-NIFTI converted data.
+ `bids/`: NIFTI-to-BIDS converted data.
+ `RESOURCES`: all 'other' (non dicom) data extracted from the XNAT server for a given subject. This could be things like the raw behavioural data from a subject, or the tech notes from the MR unit.
+ `dcm/`: sample DICOMs taken from XNAT to facilitate DICOM header comparisons. New projects will not have this folder.
+ `mnc/`: DICOM-to-MINC converted data. This is a legacy folder not found in newer projects.
+ `nrrd/`: DICOM-to-nrrd converted data. This is a legacy folder not found in newer projects.

And some other, less organized folders might be there too:

+ `clinical/`: contains *timestamped* spreadsheets of cognitive variables pulled from the Slaight Centre.
+ `bkup/`: contains any zip files obtained via FTP, email, etc, that are too valuable to throw away, but should be available already somewhere else in the directories.

**docs/**

There is no specific organization of this folder, but it is important. Any study designs, MRI protocols, relevant grant documentation, forms, guides, and miscellaneous notes can be found here.

**logs/**

This folder can be used to hold pipeline logs. These days the logs from a study's nightly datman run can instead be found in `/archive/logs/DATE-run.log`. These logs are also displayed in the QC dashboard on the main study landing page. They should be the first place to check if any data is missing or anything appears incorrect.

**metadata/**

This folder contains a lot of interesting information used by `datman` when managing the project's data.

+ `scans.csv`: This file allows us to force datman to use a particular name for a zip file, when the dicom headers have an incorrect ID. If a scan exists in `data/zips` but does not have a correctly-named symlink in `data/dicom` then an entry has to be added to this file to fix it.
+ `blacklist.csv` (Legacy file): This is a file of scans that have failed basic QC. These scans will not appear in any of the data folders and will not be run through pre-processing pipelines. For newer studies, information on blacklisted scans can instead be downloaded from the lab's [QC Dashboard](srv-dashboard.camhres.ca). For any older projects that still contain this file, the file may be missing information on scans that were created later after we introduced the QC dashboard.
+ `checklist.csv` (Legacy file): Keeps track of the qc outputs that have been looked at, and those that are still outstanding. This information is now managed by the lab's [QC Dashboard.](srv-dashboard.camhres.ca) If this file is found in an older project it may be out of date compared to the QC dashboard's database.
+ `gold_standards/`: a folder structure containing known good `.dcm` files. This is used by a header-comparison tool to ensure that vital settings (e.g., TE, TR, flip angle) are not changing between scans.
+ `protocols/`: a list of MRI protocols from CAMH and any other external sites. These should include the expected scans and most of the important MRI settings.
+ `design/`: These are files associated with the experimental design (e.g., stimulus files for fMRI tasks). Meant mostly as a backup of the code.

**qc/**

Contains, for each subject, the full QC output as an `html` page. Each folder will also have a 'manifest' file that tells the QC dashboard which metrics to display and how to display them. This allows other pipelines to easily be added to the QC dashboard overview simply by adding them to the subject folder in this directory and updating the manifest file for the subject.

**pipelines/**

Contains the outputs of all pre-processing pipelines that the lab routinely runs. If you need to work with the outputs of a pipeline and data for a subject (or multiple subjects) is missing you should contact a staff member to have them update the contents.

<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| Dawn | June 02, 2023 | Removed some legacy stuff, updated links and descriptions. |
<!-- sign-off-sheet:end -->