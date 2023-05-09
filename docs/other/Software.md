# Software

## Office

In the case that you want to use things like microsoft office on your linux machine, you simply need to log into the citrix portal.

Log in with your CAMH credentials (same as the ones you use to log into webmail etc).

Under 'non-clinical', is a folder called microsoft office 2010, which contains all of your beloved productivity softwares.

NB: When you click on any of these programs, a small 'launch.ica' program will be downloaded and launched. If this fails to open it is likely because we need to install 'citrix receiver' on your machine.

http://docs.citrix.com/en-us/receiver/linux/13-1/linux-install.html

## CIVET

A freesurfer alternative.

+ [Atlas Labels](http://www.bic.mni.mcgill.ca/ServicesSoftware/VisualGuides)

## ENIGMA DTI

DTI (pre)processing is an opinionated process with many opinions on exactly what to do. Here are some ideas. 

## Preprocessing with FSL

Certainly look to the [FDT user guide](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FDT/UserGuide).

If you're using `eddy_correct` and `dtifit` to correct and generate your tensor, we have a simple script to do the appropriate steps for an image: [dtifit.sh](https://github.com/TIGRLab/datman/blob/master/assets/dtifit.sh)

### Rotate your bvecs? 

When you run `eddy_correct` on your image, you end up rotating your volumes to align them in some way. It's an open question as to whether you should also then rotate the associated bvecs. See [our discussion](https://github.com/TIGRLab/admin/issues/508) on the topics. FSL comes with a script to do this rotation based on the log files that `eddy_correct` makes: ` fdt_rotate_bvecs`.


## The ENIGMA / JHU White Matter ROIs

Read more about the ENIGMA Protocol from [their website](http://enigma.usc.edu/protocols/dti-protocols/)

##### The ENIGMA Protocol Paper

Acheson, A., Wijtenburg, S. A., Rowland, L. M., Winkler, A., Mathias, C. W., Hong, L. E., … Dougherty, D. M. (2017). Reproducibility of tract‐based white matter microstructural measures using the ENIGMA‐DTI protocol. Brain and Behavior, 7(2), e00615. http://doi.org/10.1002/brb3.615
[PMC Full Text](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5318368/) 

Open the atlas in [NeuroVault](http://neurovault.org/collections/264/)

| Integer|Label   |Tract Name                                                                                                 |
|-------:|:-------|:----------------------------------------------------------------------------------------------------------|
|       3|GCC     |Genu of corpus callosum                                                                                    |
|       4|BCC     |Body of corpus callosum                                                                                    |
|       5|SCC     |Splenium of corpus callosum                                                                                |
|       6|FX      |Fornix (column and body of fornix)                                                                         |
|       7|CST-R   |Corticospinal tract right                                                                                  |
|       8|CST-L   |Corticospinal tract left                                                                                   |
|       9|ML-R    |Medial lemniscus right                                                                                     |
|      10|ML-L    |Medial lemniscus left                                                                                      |
|      11|ICP-R   |Inferior cerebellar peduncle right                                                                         |
|      12|ICP-L   |Inferior cerebellar peduncle left                                                                          |
|      13|SCP-R   |Superior cerebellar peduncle right                                                                         |
|      14|SCP-L   |Superior cerebellar peduncle left                                                                          |
|      15|CP-R    |Cerebral peduncle right                                                                                    |
|      16|CP-L    |Cerebral peduncle left                                                                                     |
|      17|ALIC-R  |Anterior limb of internal capsule right                                                                    |
|      18|ALIC-L  |Anterior limb of internal capsule left                                                                     |
|      19|PLIC-R  |Posterior limb of internal capsule right                                                                   |
|      20|PLIC-L  |Posterior limb of internal capsule left                                                                    |
|      21|RLIC-R  |Retrolenticular part of internal capsule right                                                             |
|      22|RLIC-L  |Retrolenticular part of internal capsule left                                                              |
|      23|ACR-R   |Anterior corona radiata right                                                                              |
|      24|ACR-L   |Anterior corona radiata left                                                                               |
|      25|SCR-R   |Superior corona radiata right                                                                              |
|      26|SCR-L   |Superior corona radiata left                                                                               |
|      27|PCR-R   |Posterior corona radiata right                                                                             |
|      28|PCR-L   |Posterior corona radiata left                                                                              |
|      29|PTR-R   |Posterior thalamic radiation (include optic radiation) right                                               |
|      30|PTR-L   |Posterior thalamic radiation (include optic radiation) left                                                |
|      31|SS-R    |Sagittal stratum (include inferior longitidinal fasciculus and inferior fronto-occipital fasciculus) right |
|      32|SS-L    |Sagittal stratum (include inferior longitidinal fasciculus and inferior fronto-occipital fasciculus) left  |
|      33|EC-R    |External capsule right                                                                                     |
|      34|EC-L    |External capsule left                                                                                      |
|      35|CGC-R   |Cingulum (cingulate gyrus) right                                                                           |
|      36|CGC-L   |Cingulum (cingulate gyrus) left                                                                            |
|      37|CGH-R   |Cingulum (hippocampus) right                                                                               |
|      38|CGH-L   |Cingulum (hippocampus) left                                                                                |
|      39|FX/ST-R |Fornix (cres) / Stria terminalis (can not be resolved with current resolution) right                       |
|      40|FX/ST-L |Fornix (cres) / Stria terminalis (can not be resolved with current resolution) left                        |
|      41|SLF-R   |Superior longitudinal fasciculus right                                                                     |
|      42|SLF-L   |Superior longitudinal fasciculus left                                                                      |
|      43|SFO-R   |Superior fronto-occipital fasciculus (could be a part of anterior internal capsule) right                  |
|      44|SFO-L   |Superior fronto-occipital fasciculus (could be a part of anterior internal capsule) left                   |
|      45|IFO-R   |Inferior fronto-occipital fasciculus right                                                                 |
|      46|IFO-L   |Inferior fronto-occipital fasciculus left                                                                  |
|      47|UNC-R   |Uncinate fasciculus right                                                                                  |
|      48|UNC-L   |Uncinate fasciculus left                                                                                   |
 


**Improper Labels**

via http://enigma.ini.usc.edu/protocols/dti-protocols/:

> **An important note **in the analysis is that the ROI labeled “IFO” is actually different that the same ROI under the most current FSL JHU Atlas label. This was different in the older JHU atlas used to create the template and make the protocols. Please note this will not play a role in ENIGMA-DTI GWAS as we will not be using this ROI for GWAS (it is very small) but it may be considered for disorder studies.

> If groups choose to use these measures for their own analysis, please be advised that this should be uncinate according to the current atlas. To avoid confusion, we will NOT switch the labels back, but we will keep this warning so you can carefully examine your data.

This shows up in the file ENIGMA_look_up_table.txt as:

45      IFO-R           Inferior fronto-occipital fasciculus right
46      IFO-L           Inferior fronto-occipital fasciculus left
47      UNC-R           Uncinate fasciculus right
48      UNC-L           Uncinate fasciculus left

whereas in the FSL JHU-labels.xml we have:

<label index="45" x="55" y="127" z="54">Uncinate fasciculus R</label>
<label index="46" x="123" y="125" z="58">Uncinate fasciculus L</label>
<label index="47" x="60" y="78" z="86">Tapetum R</label>
<label index="48" x="117" y="76" z="87">Tapetum L</label>

This means our enigma values as already computed are mislabeled. :-D

## White Matter Analysis

This page documents how we're using Lauren O'Donnell's [whitematteranalysis](http://github.com/ljod/whitematteranalysis) tract clustering tool on our data.

You should probably read the documents on her wiki to get started:
http://github.com/ljod/whitematteranalysis/wiki

**Converting PickTracts group files to Model Hierarchies in a MRML file**

That's a mouthful, but roughly the situation is this:

1. The [PickTracts](https://github.com/ljod/whitematteranalysis/wiki/Installing-Pick-Tracts) Slicer module allows you to interactively select clusters from a MRML scene file and group them.
2. Unfortunately, to run any statistics on these groups (e.g. find mean FA per tract) we need to have the groups of clusters represented using the Models plugin.
3. The Models plugin *does not* let you group clusters interactively by pointing and clicking on the image. So that sucks.

Solution: @pipitone has written a helpful script that takes a PickTracts group file, and MRML scene file, and spits out a MRML scene file with the groups represented as Model Hierarchies.

The script is here online:
https://github.com/pipitone/whitematteranalysis/blob/picktracts-converter/bin/picktracts_converter.py

To use it on the TIGR lab machines, you run:
```bash
$ module load whitematteranalysis/latest
$ export PATH=$PATH:/projects/jp/whitematteranalysis/bin/
```

Run `picktracts_converter.py --help` for usage info.

## Neural Net Libraries

This guide describes how to set up your system and GPU for neural network development using the Blocks/Theano and Caffe libraries.

**Useful Resources**

[NVIDIA CUDA Documentation](http://docs.nvidia.com/cuda/index.html)
[Blocks](http://blocks.readthedocs.org/en/latest/index.html)
[Theano](http://deeplearning.net/software/theano/index.html)
[Caffe](http://caffe.berkeleyvision.org/)
[Blocks Forums](https://groups.google.com/forum/#!forum/blocks-users)
[Theano Forums](https://groups.google.com/forum/#!forum/theano-users)
[Caffe Forums](https://groups.google.com/forum/#!forum/caffe-users)

**GPU Drivers**

Training neural networks is hard work: you definitely want to let your GPU do the heavy lifting. This section describes how to upgrade your GPU drivers to the latest stable release which works with both of the neural network libraries described below.

**Do you have a CUDA compatible GPU?**

Check whether your system has an NVIDIA GPU from the command line:

`$ lspci | grep VGA`

This will display which GPU is installed. Make sure it is CUDA compatible by verifying that it's listed somewhere here: [CUDA compatible NVIDIA GPUs](https://developer.nvidia.com/cuda-gpus)

**Upgrading Drivers**

Refer to this link for more specific info: [CUDA Getting Started](http://docs.nvidia.com/cuda/cuda-getting-started-guide-for-linux/index.html)

First, check which version of the drivers you currently have. The 311.* series of drivers may be installed by default, in which case you will need to upgrade to the current set of drivers.
`$ nvidia-smi`

We upgrade the CUDA and GPU drivers using apt-get. First, download the
[Ubuntu CUDA Network Installer](https://developer.nvidia.com/cuda-downloads) to disk, and install the cuda repository: `$ sudo dpkg -i cuda-repo-<distro>_<version>_<architecture>.deb`

Update your repository cache:
`$ sudo apt-get update`

Upgrade your cuda drivers:
`$ sudo apt-get install cuda-drivers`

Install the cuda toolkit:
`$ sudo apt-get install cuda`

Update your $HOME/.bashrc file to contain the following environment variables:
`export PATH=/usr/local/cuda-7.0/bin:$PATH`
`export LD_LIBRARY_PATH=/usr/local/cuda-7.0/lib64:$LD_LIBRARY_PATH`

Verify that the Driver Version listed in the nvidia-smi output has changed to the current driver. You may need to reboot the system.

**Miniconda**

This section describes how to install Blocks/Theano into an anaconda virtual environment environment. Download the miniconda bash install script from [here](http://conda.pydata.org/miniconda.html), and run it. Once finished with the install, create a new environment for blocks/theano:
`$ conda create -n myenv python=2.7`

Activate your new environment and install dependencies:
`$ source activate myenv`
`$ conda install hdf5 scipy h5py`

You may want to add this variable to your $HOME/.bashrc file:
`export HDF5_DIR=/path/to/your/new/conda/env/include/`

**Theano**

From your conda shell, install the bleeding-edge Theano:
`$ pip install --no-deps git+git://github.com/Theano/Theano.git`

**Blocks**

Fork and locally clone Blocks as described in their [documentation](http://blocks.readthedocs.org/en/latest/setup.html)

From the same terminal where you activated your conda environment, install Blocks locally:
`$ pip install -e git+git@github.com:USER/blocks.git#egg=blocks[test,docs] --src=$HOME \
  -r https://raw.githubusercontent.com/mila-udem/blocks/master/requirements.txt`

Set up an env variable for FUEL (Block's data library):
`export FUEL_DATA_PATH=/path/to/your/data/folder/`

For real time plotting, you may want to install bokeh:
`conda install bokeh`

**Caffe**

Caffe uses several dependencies that are not available for the anaconda virtual env, so the following section describes how to set up Caffe on your system and assumes you have sudo access. The instructions are based on the [Ubuntu Installation](http://caffe.berkeleyvision.org/install_apt.html) documentation.

All of the dependencies can be installed using `apt-get`:
`$ sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libatlas-base-dev python-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler`

Now locally clone Caffe directly from the github site: `$ git clone https://github.com/BVLC/caffe.git`

Create a copy of the Makefile.config.example file. Unless you have a specific virtual env set up for your python dependencies, or want to compile Caffe to use cuDNN, you should not need to edit it. See the [installation docs](http://caffe.berkeleyvision.org/installation.html) for details.

Make Caffe using, say, 8 threads for faster compilation:
`make all -j8`

Ensure all the tests pass:
`make test`
`make runtest`

Add the built caffe binaries to your $PATH for ease of access:
`export PATH=/path/to/caffe/build/tools:$PATH`
