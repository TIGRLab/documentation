# QSIPrep BIDS app for DWI processing
 
 QSIPrep is a BIDS App for preprocessing diffusion MRI data. It is great because, like fMRIprep, it runs from BIDS, uses cutting-edge preprocessing techniques, and outputs lots of useful QC images and QA metrics on DWI images. This page will go over the basics of using it for your own preprocessing purposes.
 

 #### The QSIPrep paper:

 Cieslak, M., Cook, P.A., He, X. et al. (2021) QSIPrep: an integrative platform for preprocessing and reconstructing diffusion MRI data. Nat Methods 18 (775–778) doi: https://doi.org/10.1038/s41592-021-01185-5

 #### For more information about QSIprep - check the help docs:

 https://qsiprep.readthedocs.io/

 
### This doc will discuss the following steps in a QSIprep workflow

 - [QSIprep] pre-processing
 - [QSIprep] reconstruction
 - [post] calculation of tensors with FSL's dtifit
 - [post] extraction of ENIGMA DTI summary tables from FSL TBSS sketelon

## BIDS

QSIprep works from BIDS converted datasets.

For projects that use the ABCD-GE 104 direction DWI sequence (i.e. the TAY cohort study, SPIN-R - SPIN30). We find the following post-steps need to be done to prepare the data.

- Update the IntendedFor (ie. any dwi fieldmap jsons should have the IntendedFor key pointing to their respective diffusion inputs)
- Make sure epi scans being used as dwi field maps don't have any non b=0 volumes (also remove `.bvec` and `.bval` files)

## Pre-processing

This is an example of the preprocessing script used for a SPIN30 - which uses the ABCD-GE 104 direction DWI sequence with reverse phase encoded b-zero fmaps for succestibility distortion correction (SDC).

```
#!/bin/bash -l

#SBATCH --partition=high-moby
#SBATCH --array=1-40
#SBATCH --nodes=1
#SBATCH --cpus-per-task=4
#SBATCH --mem-per-cpu=4096
#SBATCH --time=24:00:00
#SBATCH --job-name qsiprep
#SBATCH --output=qsiprep_%j.out
#SBATCH --error=qsiprep_%j.err

STUDY="SPN30"

sublist="/scratch/mjoseph/bids/${STUDY}/code/subject_list.txt"

index() {
   head -n $SLURM_ARRAY_TASK_ID $sublist \
   | tail -n 1
}

BIDS_DIR=/archive/data/${STUDY}/data/bids
OUT_DIR=/archive/data/${STUDY}/pipelines/in_progress
TMP_DIR=/scratch/mjoseph/tmp
WORK_DIR=${TMP_DIR}/${STUDY}/qsiprep
FS_LICENSE=${TMP_DIR}/freesurfer_license/license.txt

SING_CONTAINER=/archive/code/containers/QSIPREP/pennbbl_qsiprep_0.16.0RC3-2022-06-03-9c3b9f2e4ac1.simg

mkdir -p $BIDS_DIR $OUT_DIR $TMP_DIR $WORK_DIR

singularity run \
  -H ${TMP_DIR} \
  -B ${BIDS_DIR}:/bids \
  -B ${OUT_DIR}:/out \
  -B ${WORK_DIR}:/work \
  -B ${FS_LICENSE}:/li \
  ${SING_CONTAINER} \
  /bids /out participant \
  --skip-bids-validation \
  --participant_label `index` \
  --n_cpus 4 --omp-nthreads 2 \
  --freesurfer-input /out/freesurfer \
  --denoise_method dwidenoise \
  --unringing_method mrdegibbs \
  --separate_all_dwis \
  --hmc_model eddy \
  --output-resolution 1.7 \
  --fs-license-file /li \
  -w /work \
  --notrack
```

This is an example script for the single shell 60-direction DWI sequence commonly used at CAMH. (i.e. the SPINS study, part of PACTMD).

```
#!/bin/bash -l

#SBATCH --partition=high-moby
#SBATCH --nodes=1
#SBATCH --cpus-per-task=4
#SBATCH --mem-per-cpu=4096
#SBATCH --time=24:00:00
#SBATCH --job-name qsiprep
#SBATCH --output=logs/qsiprep_%j.out
#SBATCH --error=logs/qsiprep_%j.err

STUDY="COGBDY"

BIDS_DIR=/archive/data/${STUDY}/data/bids
FS_DIR=/archive/data/${STUDY}/pipelines/bids_apps/freesurfer
OUT_DIR=/scratch/edickie/${STUDY}_qsiprep/out
TMP_DIR=/scratch/edickie/${STUDY}_qsiprep/tmp
WORK_DIR=${TMP_DIR}/work
FS_LICENSE=${TMP_DIR}/freesurfer_license/license.txt

SING_CONTAINER=/archive/code/containers/QSIPREP/pennbbl_qsiprep_0.16.0RC3-2022-06-03-9c3b9f2e4ac1.simg

#sublist=`sed -n -E "s/sub-(\S*)\>.*/\1/gp" ${BIDS_DIR}/participants.tsv`
sublist="/scratch/edickie/${STUDY}_qsiprep/code/sublist.txt"

index() {
   head -n $SLURM_ARRAY_TASK_ID $sublist \
   | tail -n 1
}

mkdir -p $OUT_DIR $TMP_DIR $WORK_DIR

singularity run \
  -H ${TMP_DIR} \
  -B ${BIDS_DIR}:/bids \
  -B ${OUT_DIR}:/out \
  -B ${WORK_DIR}:/work \
  -B ${FSDIR}:/fsdir \
  -B ${FS_LICENSE}:/li \
  ${SING_CONTAINER} \
  /bids /out participant \
  --skip-bids-validation \
  --participant_label `index` \
  --acquisition_type singleshelldir60b1000 \
  --n_cpus 4 --omp-nthreads 2 \
  --freesurfer-input /fsdir \
  --denoise_method dwidenoise \  # MRTrix
  --unringing_method mrdegibbs \ # MRTrix
  --separate_all_dwis \
  --hmc_model eddy \
  --use-syn-sdc \
  --output-resolution 2.0 \ # mandatory
  --fs-license-file /li \
  -w /work \
  --notrack
```

To submit the above:

```sh
STUDY="COGBDY"
cd /scratch/edickie/${STUDY}_qsiprep/

N_SUBJECTS=$(( $( wc -l /scratch/edickie/${STUDY}_qsiprep/code/sublist.txt | cut -f1 -d' ' ) - 1 ))

sbatch --array=0-${N_SUBJECTS} ./code/run_qsiprep.sh
```


## Check resolution of dwi scans

Sometimes the input dwi from the study is too high quality for downstream pipelines to run. For example, if you plan to use ukftractography on your qsiprep outputs, a dwi resolution of 0.8 or 1.0 might be too high quality for ukftractography and cause it to crash. 

In these cases, qsiprep has a flag called `--output-resolution` just for these purposes. Depending on the input resolution and what you're doing with the data, it's advisable to set that argument to 1.7 or 2.0 to ensure no issues downstream.

You can check the resolution of a dwi file using fslinfo. Once FSL is loaded in, simply run `fslinfo <dwi.nii.gz>` and the resolution of the file is the `dim4` value in the output. You can also use `fslinfo` to check the output resolution of qsiprep.

## Distortion Correction

Distortion correction is an important step for cleaning up diffusion data. In order for the default distortion correction to run though, you need to make sure that the fieldmaps necessary are in the input bids dataset *and* that they are pointing to the correct diffusion data. This is done via the `IntendedFor` flag. Specifically, each diffusion fieldmap json should have a key called `IntendedFor` with the value being the relative path to its respective dwi scan. For examples, take a look at studies that have had qsiprep run on them and are correctly bids formatted (ex. CLZ, TAY, SPINS).

If you have have no usable fieldmaps for the participant(s) you wish to preprocess, you should use synthetic distortion correction which is supported in qsiprep. To use it, you need to add two flags to your qsiprep command: `--use-syn-sdc` and `--force-syn`.

## Dealing with different diffusion sampling schemes

- Split into separate files
    - sub-X_ses-X_acq-21_dwi.nii.gz
    - sub-X_ses-X_acq-22_dwi.nii.gz
    - sub-X_ses-X_acq-23_dwi.nii.gz
    - sub-X_ses-X_acq-multishell_dwi.nii.gz
- Dwi in single file

## QC Pages

For info on how to view and work with the output QC pages of qsiprep, see our guide [here](/resources/Qsiprep-QC-guide.md).

## Reconstruction and Downstream Pipelines

### reorient_fslstd

```
singularity run \
  -H ${TMP_DIR} \
  -B ${BIDS_DIR}:/bids \
  -B ${OUT_DIR}:/out \
  -B ${WORK_DIR}:/work \
  -B ${FS_LICENSE}:/li \
  ${SING_CONTAINER} \
  /bids /out participant \
  --skip-bids-validation \
  --participant_label `index` \
  --n_cpus 4 --omp-nthreads 2 \
  --recon-only \
  --recon-spec reorient_fslstd \
  --recon-input /out/qsiprep \
  --output-resolution 1.7 \
  --fs-license-file /li \
  -w /work \
  --notrack
```

### DTIFIT and TBSS

For info on how to run DTIFIT and TBSS on the outputs of QSIprep, visit [our enigmaDTI repo](https://github.com/TIGRLab/enigmaDTI) and take a look at the README!

### amico_noddi

```
singularity run \
  -H ${TMP_DIR} \
  -B ${BIDS_DIR}:/bids \
  -B ${QSIPREP_DIR}:/qsiprep \
  -B ${OUT_DIR}:/out \
  -B ${WORK_DIR}:/work \
  -B ${FS_LICENSE}:/li \
  ${SING_CONTAINER} \
  /bids /out participant \
  --skip-bids-validation \
  --participant_label `index` \
  --recon-only \
  --recon-spec amico_noddi \
  --recon-input /qsiprep \
  --n_cpus 4 --omp-nthreads 2 \
  --output-resolution 1.7 \
  --fs-license-file /li \
  -w /work \
  --notrack
```

## Studies that have been run in QSIprep

Here are some studies that have qsiprep outputs on our system already. These may be useful to you either to use the outputs yourself, or to compare what qsiprep outputs should generally look like. You can find them in the archive in their respective pipelines folders.

 - CLZ
 - SPINS
 - SPASD
 - TAY
