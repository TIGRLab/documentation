# Longterm Data Storage

To keep our storage costs low our lab likes to move data that isnt used frequently on to tape for long term storage. If you have data you won't be using for a few months or longer and don't want to delete you should move it off our projects/scratch and on to tape to help us save money :) This page describes how to do this and how to get your data back when you need it again.

## Moving data to tape
1. Organize all the data you want moved into a folder named `to_tape` in either your projects or scratch.
2. Add a README file to this folder explaining the contents for any future people who might try to use or identify it.
3. Contact a lab employee. This can be anyone who works in the lab but preferentially should be Dawn (Dawn.Smith@camh.ca) or Kevin (Kevin.Witczak@camh.ca). Just shoot an email or slack message asking for the data to be moved to tape (include the path to your `to_tape` folder).

## Retrieving data from tape
1. **Identify what you want pulled back from tape**. The records for what is stored on tape can be found at `/archive/tapes`. User data is stored by user id under the 'users' folder and specific datasets are under the 'datasets' folder. Everything is organized by *date that it was packaged* and there will be .ncdu files and .sha512 files for each item. The .sha512 files can be used to grep for a specific file/folder if you know what you're looking for and the .ncdu files can be opened and browsed with `ncdu -f <ncdu file path here>` if you're not sure what something will be named. Use the arrow keys to navigate and 'q' to quit. In some cases, data packaged on a certain date may have been split into parts (large tar files don't do well on tape!). Usually if this is the case, there will also be a file ending with `-all.ncdu` that shows the entirety of what was packaged on that date to make it easier to find things.

2. **Contact a lab employee with a list of files/folders you want**. Once you've found everything you need, send a list of files/folders to either Dawn (Dawn.Smith@camh.ca) or Kevin (Kevin.Witczak@camh.ca). If you can, include the the name of the .sha512 or .ncdu you found them in to make it easier on us :)

<!-- sign-off-sheet:start -->
<!-- sign-off-cadence:1 year -->
This shows the last time this page was reviewed to ensure it wasnt out of date.

| Name | Date | Notes |
|------|------|-------|
| Dawn | Sept 23, 2024 | Added page + review tracking |
<!-- sign-off-sheet:end -->