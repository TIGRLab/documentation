# Kimel Pipeline QC Guideline

This page is meant to provide guidance for both Kimel Staff and Researchers during quality rating/control of standard MR pipelines. It is meant to serve as a reference guide to many common issues observed during assessment of pipeline outputs. 

## fMRIPrep Anatomical

### Examples of Good Anatomical Scans

**Good BET Segmentation**

![](../_images/pipeline_qc_guide/image36.png)

**Things to look for:**
  1. Red outline (skullstrip) doesn’t include skull, outlines the brain
  2. Blue outline traces white matter area (lighter parts of brain)
**Relevant Issues**
  1. Major Skullstrip Brain Clip
  2. BET Segmentation Issue
  3. Skullstrip Clips Temporal Brain

**Good MNI Warp**
![](../_images/pipeline_qc_guide/image9.gif)

**Things to look for:**
  1. “Participant” brain only includes brain (no skull being included)
  2. Make sure that the brain isn’t being stretched down into the cerebellum (indicative of BET segmentation issue)
**Relevant Issues**
  1. MNI Warp Issues
  2. Minor MNI Edge Issues

**Good Freesurfer Reconstruction**

![](../_images/pipeline_qc_guide/image28.png)

**Things to look for:**
  1. The red outline follows the brain’s outline, the cerebellum should be excluded
  2. The blue outline follows the white matter of the brain
**Relevant Issues:**
  1. Under-inclusive Freesurfer Masking
  2. Over-inclusive Freesurfer Masking

























