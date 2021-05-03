# Who is this for?
  - Anyone who will access the data stored in the SciNet HPSS archive.
  - Anyone who needs to understand how data will be stored on SciNet HPSS.

# Preamble: SciNet’s filesystem, our archives

## HPSS

SciNet’s archival storage is located on tape, under the High Performance Storage System file system. In order to govern read and write access to tape, there are three primary access methods for this storage: two via the Slurm queue, and one [globus](https://globus.computecanada.ca/) the web interface. Although Datalad has preliminary support for globus, we’re not yet using it, so we’ll focus on the first two methods: the `vfsshort` queue and the `archive` queues.

The `archive` queues, `archiveshort` and `archivelong`, are SciNet slurm partitions, are intended for non-interactive use (though they can be used interactively), via specialised software designed for HPSS. This is how we _store_ our archive data on SciNet. The `vfsshort` is designed for interactive use via normal unix filesystem utilities. We’ll cover that here, as it pertains to using [DataLad](/data/introduction/Datalad). We’ve (or will be storing) store our archives on HPSS in the form of [Remote Indexed Archives](http://handbook.datalad.org/en/latest/beyond_basics/101-147-riastores.html), which are flat collections of optimised datalad datasets. You don’t need to appreciate the way these stores are constructed to use them, though, you just need to understand basic datalad dataset management.

Currently only POND is stored in this way, though in the future all SciNet archives will take this form. Let’s access the POND archive by way of demonstrations now.

# Installing from RIA

You’ll need a virtualenv with `datalad` installed, and only one version of python runs correctly on the archive machines, `intelpython3/2019u5`

```sh
module load intelpython3/2019u5
virtualenv --prompt='(datalad) ' ~/datalad-env
source ~/datalad-env/bin/activate
pip install datalad datalad_crawler datalad-container datalad-neuroimaging datalad-metalad git-annex-remote-globus
```

Now that we’ve got this, we can get read access the RIA store for any dataset on SciNet, begin by spawning a job in the `vfsshort` queue.

```sh
cd $SCRATCH
salloc -p vfsshort
```

```log
SALLOC WARNING:
 You have requested the default amount of time, which is only 15 minutes.
SALLOC: 1 warning was found.
SALLOC: Job submission will be attempted despite warnings.
SALLOC: Granted job allocation 5245362
SALLOC: Waiting for resource configuration
SALLOC: Nodes hpss-vfs02-ib are ready for job

```

The above error is warning you that you’ve selected the minimum amount of time (since we requested nothing), but that’s okay, since datalad installs and gets from the RIA store are generally very fast.

Now that we’re in, we can load our virtualenv in the usual way:

```sh
source ~/datalad-env/bin/activate
```

In this machine on `vfsshort` you have access to an additional filesystem mount, located at `/archive/a/arisvoin/[username]`. It is conveniently provided to you with the shell variable `$ARCHIVE` as well.

SciNet’s queue system automatically defaults to setting your current working directory as the directory the job was launched from, *iff* that directory is available on the job node (in this case, `$SCRATCH` is available in both places, so we’re still there).

As you’ll remember from [previous datalad tutorials](/data/introduction/Datalad), install operations on correctly formatted datasets are generally quite fast, because only only text files of 1kB or smaller are actually copied.

Installing from an RIA store has a slightly different syntax than installing from a normal dataset, but it’s fundamentally very similar. Let’s get POND now:

```sh
datalad install -s ria+file://$ARCHIVE/POND_RIA#~POND
```

In this case, we’re telling it to look, in the `$ARCHIVE`/POND_RIA path, for an aliased dataset ID called POND, which is the superdataset of the entire POND dataset on SciNet for us.

```log
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
configure-sibling(ok): /scratch/a/arisvoin/kwitczak/demo/POND (sibling)
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND (dataset)
action summary:
  configure-sibling (ok: 1)
  install (ok: 1)
```

Now we have the POND dataset installed in our `$SCRATCH` directory. It’s installed but no data has been gotten. Remember that access to `$ARCHIVE` is contingent upon being inside a slurm job in the `vfsshort` queue. This if we `exit` and relinquish the slurm job now, we won’t be able to `datalad get` our data when we are outside on the normal SciNet nodes. This, if we want to `datalad get` this data, we have to do it now, or in some future `vfsshort` job. Let’s get some now:

```sh
datalad get data/baseline/bids/sub-0880002 \
            data/baseline/bids/sub-0880019 \
            data/baseline/bids/sub-0880020 \
            data/baseline/bids/sub-0880023 \
            data/baseline/bids/sub-0880024 \
            data/baseline/bids/sub-0880043 \
            data/baseline/bids/sub-0880044 \
            data/baseline/bids/sub-0880046 \
            data/baseline/bids/sub-0880049 \
            data/baseline/bids/sub-0880050
```

```log
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880002 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880002]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880019 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880019]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880020 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880020]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880023 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880023]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880024 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880024]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880043 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880043]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880044 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880044]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880046 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880046]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880049 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880049]
[INFO   ] Scanning for unlocked files (this may take some time)
[INFO   ] Reconfigured POND_RIA-storage for ria+file:///archive/a/arisvoin/kwitczak/POND_RIA
[INFO   ] Configure additional publication dependency on "POND_RIA-storage"
install(ok): /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880050 (dataset) [Installed subdataset in order to get /scratch/a/arisvoin/kwitczak/demo/POND/data/baseline/bids/sub-0880050]
get(ok): data/baseline/bids/sub-0880002/ses-01/anat/sub-0880002_ses-01_T1w.json (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/anat/sub-0880002_ses-01_T1w.nii.gz (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/anat/sub-0880002_ses-01_T2w.json (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/anat/sub-0880002_ses-01_T2w.nii.gz (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/dwi/sub-0880002_ses-01_acq-singleshell19dir_dwi.json (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/dwi/sub-0880002_ses-01_acq-singleshell19dir_dwi.nii.gz (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/dwi/sub-0880002_ses-01_acq-singleshell20dir_dwi.json (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/dwi/sub-0880002_ses-01_acq-singleshell20dir_dwi.nii.gz (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/dwi/sub-0880002_ses-01_acq-singleshell21dir_dwi.json (file) [from POND_RIA-storage...]
get(ok): data/baseline/bids/sub-0880002/ses-01/dwi/sub-0880002_ses-01_acq-singleshell21dir_dwi.nii.gz (file) [from POND_RIA-storage...]
  [176 similar messages have been suppressed]
action summary:
  get (ok: 186)
  install (ok: 10)
```

We just grabbed these subdatasets. When we `exit` the slurm job we will now have these data to work with.

# Coming soon: MORE ...
