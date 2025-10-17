# Conda

Although several Python bundles have been built as mentioned above, users with
specific requirements for Python should install their own version by using
Conda. Note that conda is given as a module Brief instructions for this are given below.

Note that this guide is a very short introduction to its basic/initial use. It
is not intended to be a full user guide for installing and using Conda. For
that, see the 
[online reference guide](https://conda.io/projects/conda/en/latest/user-guide/index.html).

## Environments

Conda allows the user to create separate environments containing files,
packages, and their dependencies which will not interact with other
environments. As alluded to in the information given above, a default 'base'
environment will be activated by default as part of the load procedure for each
new shell. How to change this default behaviour is covered below. First,
however, the default 'base' environment must be activated for the current
shell, either by starting a new shell or sourcing the `.bashrc` file:

```
$ source ~/.bashrc

(base) $ conda info

active environment : base

active env location : /home/abc123/miniconda3

shell level : 1

user config file : /home/abc123/.condarc

...

offline mode : False

(base) $
```

Note that the command line prompt changes to indicate that the user is now
working within the specified environment.

Now, to turn off auto activation of the default base environment:

```
(base) $ conda config --set auto_activate_base false

(base) $

The active Conda environment can be deactivated when finished using the
'deactivate' command:

(base) $ conda deactivate

$
```

If `auto_activate_base` was set to false then the default base environment can
be activated by calling the `activate` command directly:

```
$ source miniconda3/bin/activate

(base) $
```

Other environments can be created using `conda create` and activated using
`conda activate`. Available environments can be listed using `conda info`:

```
(base) $ conda create --name ProjX

...

(base) $ conda activate ProjX

(ProjX) $ conda info --envs

conda environments:

base /home/abc123/miniconda3

ProjX * /home/abc123/miniconda3/envs/ProjX

(ProjX) $ conda deactivate

(base) $
```

Note that it is prudent to updated the initial base environment, as described
below, before adding any new environments to the installation since any update
only affects the current, active environment.

## Packages

Even if the version of Conda which was downloaded and installed was the
latest version listed on the download web page, some of its constituent
packages may have been updated since that download was built. To ensure that
the current package versions are the latest available, all packages should be
updated. This can be achieved using the 'conda update' command by giving the
extra option `--all`. Note, however, that this will only update the packages
in the current environment, not all existing environments within the
installation. If more environments have been added then the update must be
repeated in each one. As mentioned above, it is therefore prudent to updated
the initial base environment before adding any new environments:

```
(base) $ conda update --all

Collecting package metadata (current_repodata.json): done

...

The following packages will be downloaded:

...

The following packages will be REMOVED:

...

The following packages will be UPDATED:

...

The following packages will be DOWNGRADED:

...

Proceed ([y]/n)? y

Downloading and Extracting Packages

...

Preparing transaction: done

Verifying transaction: done

Executing transaction: done

(base) $
```

The `conda install` command can be used to install further packages, or a
requirements list can be supplied:

```
(base) $ conda install numpy scipy

...

(base) $ conda install --file requirements.txt

...

(base) $
```

Specific package versions can also be specified. See the conda online reference
for further details.

### Jupyter Notebook

The classic Jupyter Notebook can be installed from the Navigator launch page,
or added using:

```
(base) $ conda install -c conda-forge notebook

...
```

See the [online guide for further details](https://jupyter.org/install)
