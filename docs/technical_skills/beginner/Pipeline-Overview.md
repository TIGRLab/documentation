# How to Run Pipelines

### What are pipelines? (BIDSApps)
Pipelines are the workflows that we use in the lab to preprocess the raw data we get from scanners to make it usable for analysis. Some examples of what pipelines do are registering the data to templates for comparison, doing motion correction, and extracting metrics for each participant on the quality of the data.

BIDSapps are a subset of pipelines that run directly from BIDS-formatted datasets. We prefer using these pipelines because of their robustness, the consistency that BIDS-formatted datasets and pipelines provide, and that they are widely accepted and actively worked on by the science community.


 - [mriqc](https://mriqc.readthedocs.io/en/stable/): Extracts image quality metrics on anatomical and functional data. Usually run first for any data coming in to get metrics on motion and image quality.
 - [fmriprep](https://fmriprep.org/en/stable/): Full preprocessing pipeline for fMRI data on the lab, does coregistration with anatomical data, normalization, unwarping, etc. Makes fMRI data usuable for analysis.
 -  [qsiprep](https://qsiprep.readthedocs.io/en/latest/): Full preprocessing pipeline for dMRI data, with motion correction, bias field correction, and coregistration with anatomical data. Makes dMRI data usable for analysis.


These pipelines are created using [Nipype](https://nipype.readthedocs.io/en/latest/), which is a python framework that allows you to organize code and commands into workflows that can connect with each other easily. Nipype, as a framework, has a lot of other benefits behind the scenes like caching steps of a pipeline and running steps in parallel automatically. For our use, it's mainly important to know that most of our pipelines that we use in the lab are written in Python, which is why we will be installing them with python environments and containers with python installed later on.

## How To Run Pipelines Locally

*Here's a quick overview for how to run bids apps. using pyenv or singularity containers, using fmriprep as an example.*

### Running the pipeline via a singularity container

#### Purpose of Containers


The technical definition for containers are operating system virtualization, **but** you can think of them as a literal virtual container. A container, in plain terms, is a file that can hold an operating system, data, software, and your own code altogether, and then can be accessed as if it was it was  software itself. For example, you can have an fmriprep container which holds a specific version of fmriprep and then you can run the container file itself to use that fmriprep on any data you want. 

While this may seem redundant at first (why wouldn't you just use that software as intended on your own system?), there are many benefits to using containers:

 - Consistency: Once you have created a container with specific software inside, all of those dependencies are frozen with that version of the software forever meaning you can ensure reproducibility.
 - Mobility: Containers can be compressed to a single file that are often smaller than the contents of the software you initially put in it. This means you can send it to other systems  and collaborators easily, and they can run the container themselves.
 - Flexibility: Since can put anything in a container that you would in any system, this can be extended to creating containers with your custom made code to publish as ready-to-use software.


As some preliminary knowledge on how we use containers in our lab, you just need to know that we mainly use Docker for pulling and creating containers, Singularity for running containers, and an **image** is a compressed container file (.simg files means its a singularity image).

#### Building containers

*Note: For most use cases on running pipelines, you don't need to build your own container. Most of the time, the container you need for running your pipeline should already be available in `/archive/code/containers`.*

```
docker pull nipreps/fmriprep:latest
docker run --privileged -t --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /path/to/output/for/container:/output \
  singularityware/docker2singularity \
  nipreps/fmriprep:latest
```

#### Running a singularity container
Now let's try binding the data to a singularity container, and then run it!

```
bids_dir=/your/bids/dir
output_dir=/your/output/dir
freesurfer_license=/path/to/freesurfer/license.txt
sing_img=/archive/code/containers/FMRIPREP/nipreps_fmriprep_20.2.6-2021-10-28-c177df20c804.simg
sub="sub-CMH0028"
singularity run -H /your/singularity/home/dir \
  -B ${bids_dir}:/bids \
  -B ${output_dir}:/out \
  -B ${freesurfer_license}:/li \
  --skip_bids_validation
  ${sing_img} /bids /out participant --participant-label ${participant}
```


### Running the pipeline with a custom environment

#### Modules 

Many pipelines mentioned all use some sort of software for each preprocessing step. For example, fmriprep needs FSL to run many of its steps. On our system, you need to load in that software using our module system. If you haven't heard of that before, it's recommended you read [this documentation on how to use modules](http://imaging-genetics.camh.ca/documentation/#/introduction/Modules?id=modules). For our fmriprep example, you would have to load in FSL like the following:
```
module load FSL
```

#### Python Virtual Environments (pyenv and virtualenv)

Many bids-apps pipelines, especially the ones developed by [nipreps](https://www.nipreps.org/), are based on python in some way (fmriprep, mriqc, qsiprep). An issue that you can run into is loading multiple programs/pipelines into your default python environment, as having multiple pipelines in one environment can cause conflicting dependencies.

The way to remedy this issue is to use python environments! They're exactly what they sound like: instead of having a singular python environment that you install everything in, you can create multiple independent python environments that you can load in and work with depending on your specific use case. Ie, you can have separate python environments for fmriprep, qsiprep, tbss, each with their own dependencies and python versions if necessary.

Let's go through how to get python environments on our system, using pyenv and virtualenv (you can also use other python environment options such as [miniconda](https://docs.conda.io/en/latest/miniconda.html))

Install pyenv and virtualenv, following [this tutorial](https://github.com/pyenv/pyenv-virtualenv). If you need exactly what to put in your `.bashrc` file, then use this:

```
export PYENV_ROOT="/my/pyenv/root/folder"
export PATH="$PYENV_ROOT/shims:$PATH"
export PATH="$PYENV_ROOT/bin:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
```

With this, you can install pipelines directly into a python environment. For example, to make an fmriprep environment, do the following:

```
# Pull fmriprep
git clone https://github.com/nipreps/fmriprep
# Go into the fmriprep directory
cd fmriprep

# Activate your bashrc to install 
source /where/you/wrote/.bashrc
# Install a version of Python if you haven't already
pyenv install 3.7.3

# Create an fmriprep environment based on that python environment
pyenv virtualenv 3.7.3 fmriprep
# Activate the fmriprep environment
pyenv activate fmriprep
# Install fmriprep in the environment
pip install .
# Or
python setup.py install

# Check if it is installed
fmriprep --help
```

And with that, you have the pipeline installed in your own python environment! You can do this with any python module repo: mriqc, dmriprep, datman, etc.

### Running the pipeline on data

Now that you have the pipeline set up, you can run it on some data. Let's try it on the ASCEND dataset.

```
# Test fmriprep by checking the usage log
fmriprep --help

# This is an example fmriprep run
fmriprep \
  <bids_dir> \
  <output_dir> \
  participant \
  --participant-label <PARTICIPANT_LABEL>
```

## Running Pipelines on our Queue

### TIGR-PURR

*If you're wondering how to run standard bids-apps pipelines on datasets in our lab, start here!*

Tigr-purr is a handy way to run pipelines if they are already configured with the correct config and descriptor files. Most of our major bids apps and pipelines are already set up in nextflow (mriqc, fmriprep, qsiprep) so it's just a matter of collecting your inputs and the relevant nextflow config files, and then the nextflow command takes care of the rest! You can even write your own nextflow pipelines for longer term, robust projects. For more info, see our full tigrlab nextflow documentation [here](https://tigrlab-nextflow-personal.readthedocs.io/en/latest/).

For a short "get-started" example, here's an example of running mriqc on a bids dataset with nextflow.
```
ml tigr-purr
nextflow /archive/code/tigrlab_nextflow/bids.nf \
  -c /archive/code/tigrlab_nextflow/nextflow_config/mriqc-0.14.2.nf.config \
  --bids /archive/data/TAY/data/bids \
  --out /projects/smansour/TAY/mriqc \
  -profile local
```


### Slurm
Slurm is a job-scheduler made for organizing and running pipelines on HPC (high-performance computing) clusters. You can find out more about how this queue works [here](http://imaging-genetics.camh.ca/documentation/#/introduction/Queue-basics). This is the heart of how we are able to manage the different pipelines we have running across everyone in the lab in an optimal matter. Note that **tigr-purr is a wrapper around slurm**, ie you can use tigr-purr to submit your jobs with its extra conveniences (config files already set up for our major pipelines), or you can interface with slurm directly.

In practice, this is done through writing **slurm scripts**. These scripts contain the code you want to run for each job, as well as extra information in the header that slurm needs for resource allocation. This includes the expected runtime, how much memory is required, and how many cpus are needed for each job.

Keep in mind that writing your own slurm scripts is an **alternative** to tigr-purr. It is recommended to try tigr-purr itself for high reproducibility (using same params as everyone else) and ease of use, but there will always remain use cases for your own slurm scripts. For example, you would want to write your own slurm script if your desired pipeline is not setup in tigr-purr or if it's a custom job that isn't widely applicable to the lab (like analysis scripts) and therefore generally not worth implementing in nextflow for the future.

On top of reading [our documentation for writing slurm scripts](http://imaging-genetics.camh.ca/documentation/#/technical_skills/beginner/Writing-Slurm-scripts), the best way to understand how to get started with slurm scripts is to look at one.

`run_fmriprep.sh`:
```
#!/bin/bash -l

#SBATCH --partition=high-moby
#SBATCH --array=1-5
#SBATCH --nodes=1
#SBATCH --mem-per-cpu=4096
#SBATCH --cpus-per-task=4
#SBATCH --time=24:00:00
#SBATCH --job-name fmriprep
#SBATCH --output=fmriprep_%j.txt

cd $SLURM_SUBMIT_DIR

sublist="/your/study/sublist.txt"

index() {
   head -n $SLURM_ARRAY_TASK_ID $sublist \
   | tail -n 1
}
sub=`index`

BIDS_DIR=/your/bids/dir
OUT_DIR=/your/output/dir
sing_img=/fmriprep/sing.img

mkdir -p $BIDS_DIR $OUT_DIR

singularity run \
  -H /your/singularity/home/dir \
  -B ${BIDS_DIR}:/bids \
  -B ${OUT_DIR}:/out \
  -B /your/freesurfer/license.txt:/li \ \
  /bids /out participant \
  --participant_label ${sub} \
  --fs-license-file /li \
  --n_cpus 4 --omp-nthreads 2 \
  --output-spaces T1w MNI152NLin2009cAsym fsaverage
```

This script runs fmriprep on five subjects from the input BIDS dataset `/your/bids/dir`, and outputs it to `/your/output/dir`. The fmriprep container is `/fmriprep/sing.img`, and the list of subjects is `/your/study/sublist.txt`. This is a basic template for how slurm scripts on BIDS datasets will generally look like, and you can start from this and swap out the parameters to get it working for your purposes. For example, you can change the container and command to run qsiprep instead, or change the subject list and bids dataset to use different input data.

**Note:** The sublist is just a list of subjects in a text file, using their bids name, one subject per line. 
For example:
```
sub-CMH0019
sub-CMH0028
sub-CMH0029
sub-CMH0032
sub-CMH0040
```

### Submitting your jobs to the queue

Once you have your slurm script all good to go, you submit it to slurm using `sbatch`, ie 
```
sbatch <slurm_script>
```

You can check up on the status of all jobs running on our system by doing:
```
squeue -a
```

Or only focus on your job statuses by doing:
```
squeue -u <username>
```

You can cancel your jobs by doing:
```
scancel -u <username>
```
