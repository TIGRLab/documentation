# Modules

Modules are a neat way to allow multiple (possibly conflicting) versions of the same software to be installed. They also help make analyses more reproducible by allowing you to explicitly state which versions of what software you want to use.

## Basic commands

- `module avail`: Shows you all available software
- `module list`: Shows you which modules you're currently using. We pre-load some modules for our users that are really useful and not likely to interfere with anything (e.g. slurm to allow users to use our queue).
- `module load <modulename>`: Will let you use software that we have available. For instance, if you typed `module avail` and saw that we had FSL/5.0.0, FSL/5.0.10, and FSL/6.0.0 installed you could use `module load FSL/5.0.10` to load version 5.0.10. If you omit the version number either the newest version or the one marked 'default' will be loaded for you (e.g. `module load FSL` would have loaded FSL/6.0.0). **Be Careful!** If you rely on this feature the versions that are current / defaults could change out from under you. It's always better to specify exact versions.
- `module unload <modulename>`: Lets you unload software once you're done with it
- `module swap <modulename>`: Lets you change module versions. E.g. `module swap FSL/5.0.11` when you have 6.0.0 loaded will switch to the older version without you first having to unload 6.0.0.

There are also other commands available and various options you can set. You can see the full list of options by running `module help`

## Accessing our SCC modules

Our lab's modules aren't available by default on the SCC. To add them to the `module avail` list you first need to run `source /KIMEL/tigrlab/quarantine/scc_modules.sh` or `source /KIMEL/tigrlab/quarantine/kimel_modules.sh`. The first command (scc_modules.sh) gives you access to all our of SCC-only modules. These are all guaranteed to work on the SCC, but are probably (not always) more out of date than our lab's modules. The second command (kimel_modules.sh) gives you access to all of the modules that are available on our workstations in the lab. Most of these will work just fine on the SCC, but some may have software dependencies that are missing on the SCC nodes. If there is a module you need that doesnt work on the SCC, or if you need software that doesnt have a module, please let us know and we'll help!

## Private Modules

Private modules are like regular modules but they're available only to the user that makes them. This means that users who are not system admins can install _some_ software of their own and load it as they need it. If you think you might need this, you can learn more at [Private Modules](/resources/Private-Modules)

## Additional Info

Knowing more isnt necessary to use the modules effectively, but if you're curious you can learn more about modules from [lmod's documentation](https://lmod.readthedocs.io/en/latest/). The module files we use are stored at `/opt/quarantine/modules/` if you want to see them. They can be written in [TCL](https://en.wikibooks.org/wiki/Tcl_Programming/Introduction) or [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)).
