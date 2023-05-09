# Freesurfer Outputs on the Kimel Lab server

1. [What is freesurfer](#what-is-freesurfer)
2. [What is dm-proc-freesurfer.py](#what-is-dm-proc-freesurferpy)
3. [Pipeline outputs](#recon-all-output-folder-structure)
4. Disclaimer...how to QC the freesurfer outputs before going forward. 
     1. [Check the checklist](#check-the-checklist)
     2. [Understand what version of freesurfer was run and on what system](#understand-what-version-freesurfer-was-run-and-on-what-system)
     2. [Look at the visual QC pages](#look-at-the-visual-qc-pages)
     3. [Check for outliers when you are running your stats](#check-for-outliers-when-you-are-running-your-stats)
5. [Tutorials for freesurfer group analysis](#tutorials-for-freesurfer-group-analysis)

## What is freesurfer

> FreeSurfer is a software package for the analysis and visualization of structural and functional neuroimaging data from cross-sectional or longitudinal studies. It is developed by the Laboratory for Computational Neuroimaging at the Athinoula A. Martinos Center for Biomedical Imaging.

When most people think of the "freesurfer pipeline", they are referring to freesurfer's "recon-all" pipeline. [Recon-all](https://surfer.nmr.mgh.harvard.edu/fswiki/recon-all) is a 31 stage pipeline that does everything from T1-weighted image preprocessing, to cortical surface extraction, to segmentation of the cortex (DKT atlas) and subcortical structures (Freesurfer "automatic segmentation" or aseg). 

Luckily, Freesurfer also has a very detailed wiki describing all that it does and including many example tutorials. https://surfer.nmr.mgh.harvard.edu/fswiki/FreeSurferWiki. A good place to start is [this freesurfer overview] (https://surfer.nmr.mgh.harvard.edu/fswiki/FreeSurferAnalysisPipelineOverview).

### How to run freesurfer

There are two main ways to go about getting freesurfer outputs for your needs. 

**For cross-sectional data:** Use the freesurfer run that is generated automatically via other pipelines, ex. fmriprep and qsiprep. When you run fmriprep/qsiprep on a bids dataset, it will automatically run freesurfer for all applicable scans (anatomical, functional, etc.) This is convenient as you just need to run the main preprocessing pipeline of your choice and it will take care of any freesurfer outputs necessary.

**For longitudinal data:** Use the freesurfer bids app. The reason for this is that freesurfer will do calculations on subsets of the sessions available for each participant in the dataset. For example for a participant that ends up collecting multiple sessions, it will have unique freesurfer outputs for {ses-01}, {ses-01, ses-02}, {ses-01, ses-02, ses-03} as more sessions get added to the input. So, for longitudinal data, make sure you end up running freesurfer for the set of sessions you need for analysis for the most accurate results.

### recon-all output folder structure

The "recon-all" output is not one file...but a directory tree of ~200 files (amounting to ~300MB). Freesurfer software uses a collection of them to run and expects them to be organized exactly as recon-all created them...or things get very messy, very fast. This is because features like surface geomentry and cortical thickness are stored in separate files. Some useful explanations of the whole recon-all output can be found here at these links:

+ A table of all recon-all output files and which steps they come from:
  + freesurfer version 5.3 and below (http://grahamwideman.com/gw/brain/fs/fsunderstanding2006/processvsdata2006.htm)
  + freesurfer version 6.0 (https://surfer.nmr.mgh.harvard.edu/fswiki/ReconAllTableStableV6.0)
+ An additional table of option (non-default) recon-all all options and the files they produce. (https://surfer.nmr.mgh.harvard.edu/fswiki/OtherUsefulFlags)
+ The recall-all usage page (https://surfer.nmr.mgh.harvard.edu/fswiki/recon-all)
+ Another (colorcoded!) table of freesurfer steps and output files (http://grahamwideman.com/gw/brain/fs/fsunderstanding2006/processvsdata2006.html)

## How to QC the freesurfer outputs before going forward. 

dm-proc-freesurfer.py is creating new freesurfer outputs automatically for you.. **You are responsible for quality control of the freesurfer outputs you incorporate into your analyses**. Some simple steps for quality control are listed below.

### Check the checklist

The freesurfer-checklist.csv file inside the projects output folder is your first signal that something might have gone wrong..

#### Situation 0 - there is no nifti inputs on our file system yet

Sometimes there can be errors/delays in our automatic file transfer system that cause a subjects file to not be uploaded and converted in a timely manner. If you are expecting a participants data, but do not see raw data for that participant in the /archive/data/<project>/data/nii/<subject_id> folder. Than contact a member of the TIGRlab admin team (and file an issue at: https://github.com/TIGRLab/admin/issues). 

#### Situation 1 - the raw data has not been QCed

Most TIGRlab data is set up to not be processed unless the raw data has been QCed and signed off on (using the QC- dashboard).  If a T1w nifti file exists in the file system, but no entry for this participant is present in the freesurfer-checklist.csv, than it means that this data has not been QCed.  Contact the QC contacts responsible for that project. [QC contacts are listed here](https://docs.google.com/spreadsheets/d/1MTuU7Y7hqAHbiHMP2NsnrW1wZ9UZrmz4AQpJCQ1-_BQ/edit?ts=58c06d8b#gid=0)

#### Situation 2 - no T1 input found

The computer looked in the subjects nifti folder and found no T1w image.  It prints "no _T1_ found" to the column of the freesurfer folder and quits. If a T1w file should be there...than investigate. Make sure that all T1w data from this participant has not been blacklisted.

#### Situation 3 - too many T1w files found

In many cases, dm-proc-freesurfer.py will not run freesurfer it you give it find more that 1 T1w Image for a participant.  When it does this it will print ("> 1 _T1_ found") to the freesurfer-checklist.csv.

You have some personal choices for how to deal with this problem:
1. Select one T1w Image you would like freesurfer to run. This is the best solution is one of the T1w Images collected is of significantly poorer quality than another. The select and image you have two options.
  1. Manually edit the T1_nii column in the freesurfer checklist with the file name of the selected file.
  2. Blacklist and delete the poor quality file from the input nii folder. (the TIGRlab has a preference for this option)
2. Have freesurfer take both T1w Images as it's input. If neither scan from the participant is of ideal quality, a better option might be to submit them to the freesurfer pipeline together (so that freesurfer can work from the average of the two scans). To do this, manually update the freesurfer-checklist.csv with BOTH filenames, separated by a semi-colon (;).  
 

### Understand what version freesurfer was run and on what system

A message from the freesurfer developers (https://surfer.nmr.mgh.harvard.edu/fswiki/DownloadAndInstall):
> **Important Note**: When processing a group of subjects for your study, it is essential to process all your subjects with the same version of FreeSurfer, on the same OS platform and vendor, and for safety, even the same version of the OS. While we continue to work to ensure that results match across platforms, there are none-the-less system-level libraries that are OS dependent. An exception to this rule is that you may view and edit files across any platform or version, and run some post-processing tools (outside the recon-all stream) if you check with us first (for instance you may run the longitudinal processing with newer versions).

Luckily, freesurfer leaves some detailed logs in the freesurfer output structure so that you can double check 1) what version of freesurfer was run and which system and 2) that this is true for all subjects in your analysis.

The best place to check is the recon-all.done file found in:
```
/archive/data/<project>/pipelines/freesurfer
└── subject_id 
     └── scripts 
         └── recon-all.done 
```

An example recon-all done file looks like this:
```
------------------------------
SUBJECT 50002_baseline
START_TIME Thu Sep 29 19:52:28 EDT 2016
END_TIME Fri Sep 30 07:19:42 EDT 2016
RUNTIME_HOURS 11.454
USER edickie
HOST gpc-f125n009-ib0
PROCESSOR x86_64
OS Linux
UNAME Linux gpc-f125n009-ib0 2.6.32-504.3.3.el6.x86_64 #1 SMP Wed Dec 17 01:55:02 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
VERSION $Id: recon-all,v 1.379.2.73 2013/05/12 23:15:37 nicks Exp $
CMDPATH /home/a/arisvoin/edickie/quarantine//freesurfer/5.3.0/build/bin/recon-all
CMDARGS -subject 50002_baseline -i /scratch/a/arisvoin/edickie/ABIDEII/inputs/ABIDEII-UPSM_Long/50002/baseline/anat_1/anat.nii.gz -sd /scratch/a/arisvoin/edickie/ABIDEII/FSout/UPSM_Long -all
```
The last four lines are most important:
 + **UNAME** - the operating system it was run on
 + **VERSION**  - gives you the freesurfer version
 + **CMDPATH** - gives your the full path to the recon-all pipeline script
 + **CMDARGS** - gives you the full call to the recon-all pipeline that was run (including inputs and optional arguments.
 
### Look at the visual QC pages

QC pages for freesurfer are generated AFTER the recon-all outputs are converted from freesurfer format to HCP format. For this reason, the visual qc pages for freesurfer recon-all pipeline are actually sitting in inside /archive/data/<project>/piplines/freesurfer/hcp.

More information on how to inspect these outputs is in the ciftify wiki:

### Check for outliers when you are running your stats

Before you run group stats. Plot histograms and check for outliers. Some outlier values could be signs of a poor segmentation and indicate that the subject should be excluded.  When you see an outliers it is best to inspect the data again (i.e. re-check the visual qc mentioned above)

## Tutorials for freesurfer group analysis

If you want to run vertex-wise analysis (i.e. you want to search through the brain for areas were thickness is associated with you predictors) you need the full recon-all outputs. Which should be saved somewhere by the lab.  There is some software for running the GLM built into freesurfer...find some info about it here (https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial/GroupAnalysis), there is also a newer software for running GLM's with permutation test called PALM that can read freesurfer surfaces, (or cifti files, that you would use after doing my conversion script. More info about PALM can be found here (https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/PALM)