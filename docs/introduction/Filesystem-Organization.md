# Filesystem Organization

This page will tell you how our files and folders are organized so you can more easily find things.

- `/projects` and `/scratch`: Every active user will have folders in each of these locations that are named after their lab account. You should keep all of your files in these directories. They're centrally managed, and available on every workstation and the SCC (i.e. your user folder contents is the same on every single computer). Both directories get backed up nightly and we have about 30 days worth of snapshots available to restore files if something mistakenly gets deleted.
- `/home/${USERNAME}`: Your home folder, which is **unique on each machine**. While your `/projects` or `/scratch` are shared between machines the `/home` folders on each machine are local to that machine. This means **anything in your home folder on workstation X will not be available on workstation Y**. All of your files should be kept in `/projects` or `/scratch`. This is particularly important for the queue because when your job is sent to another node of the cluster it won't be able to see any of the files **you** can see in your home folder and your job will fail! It's also important because if that workstation's hard drive dies you'll lose everything you have in your home directory for good (no backups are kept).
- `/archive/data`: Contains data from past and current studies that the lab manages. This is another one of our directories that is available from the SCC. See [Data Organization](/data/Data-Organization) and [Data Naming](/data/Data-Naming) for information on how the data in our archive is organized (in great detail!) if you would like to know more.
- `/archive/code`: We store copies of our lab created code (e.g. datman, ciftify and the dashboard) here for use on the archive data.
- `/archive/code/config`: This is where we store the bash scripts + config scripts for our archive. The `<study>_management.sh` scripts describe exactly what we're running on each study every night. The `<study>_settings.yml` files detail which types of scans are collected per site for each study that we're managing. You can look at these to get an overview of what data we should have in each study folder.
- `/external`: Contains data from publicly-available data sets and data from other hospitals. The sub-folder `admindocs` holds all of the access documents for our public data sets. Some of our public data sets have been moved to Scinet. If this is the case there will be a README file left behind in their folder indicating how to find them.
- `/archive/resources`: This contains some atlases we have available, some of our clinical data and a guide to using our MRS analysis pipeline.

## The SCC

From the scc's nodes you can access our system's archive, scratch, projects and quarantine modules. They're all found in the `/external/rprshnas01/tigrlab` directory.



<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| TIGRLab | April 24th, 2023 | Reviewed contents, looks fine. |
| Dawn | Sept 23rd, 2024 | Reviewed contents. No updates needed |
| Dawn | July 29, 2025 | Added the SCC folder location |
| Dawn | Apr 21, 2026 | Reviewed and edited contents. |
<!-- sign-off-sheet:end -->