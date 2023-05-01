## Who is this for?
   - Anyone who wants to create work that is sharable / properly archivable!
   - Anyone who is going through the process of adding a study to `/archive`

## Best practices
We strongly recommend that you document your research in a standardized way. Below are some recommendations for how to go about that. We try to adhere to this ourselves where possible. Following this makes it easy for us to archive your data if you should leave the lab and makes it easier for other lab members to access and interpret your work.

Ideally each project you work on should have its own folder. This folder should contain:
1. a folder named `bin` or `scripts` containing all code needed to replicate your project.
2. Final spreadsheets or csvs of concatenated (“cleaned”) variables. (i.e. the thing you actually used to do the stats and make the figures)
3. A README.md  file that will provide future trainees with the information they need. For more information of the contents of a README file see the “README.md Contents “ section below.

## README file format
Data should be accompanied by a `README.md` file containing the information below.

### Date Collected/ Processed:

* Include a date or range that indicates when the data was collected or what date the processing steps were applied 

### Dataset: 

* Brief summary of dataset/study
* Description of how data is acquired
* What each folder contains 
* Pointers to external data used
* Location of the raw data (BIDS Dataset the processing pipelines used)
* Inclusion Exclusion Specific to this analysis  (i.e. all data collected as of..., all data for which this other variable was available...)
* Pointers to files or documents with any of this information also works

### Contact People (Include name and email addresses) 
* Principal Investigator
* Primary: (the person who ran the data)
* Others: (the person(s) who else we might be able to ask questions of if the Primary is not reachable)
* Citation and authorship requirements if this data is used

### System on which data was run: 
* (SciNet, TIGRlab, other?)
* Environments Used (minc environment? Freesurfer? FSL?, which version of each?)
 * If you are using code from within a git repository – you might want to include the id of the commit
 * Dependencies or programs that had to be loaded to run the scripts/pipeline

### Commands and Scripts Run: 
* If you are using code from within a git repository – you might want to include the id of the commit
* List all scripts that are included in the bin/scripts folder, in the order that they were run
 * beside each script, add a comment about what each script does
* You should make mention of any template files, atlases or masks used 
* if these masks are not publicly available, or easy to find they should be included with the backup
* For BIDsapps, can point to invocation files and nextflow reports that show the parameters and commands used to generate pipeline outputs

### Quality Control:
* Who did quality control?
* Date Quality Control was completed
* Link to the QC file

### Data Requests/Publications
* List any Data Requests or Publications that have been made using the dataset
* Include who made the data request, the date and relevant citations if applicable 

### Specific Notes: 
Anything else you think a future lab member should know about this analysis before calculating new values

