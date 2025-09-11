# Omics Server Documentation

This repository holds documentation for both the users and admins of Pigeon
and Manny.

These pages can be accessed
[here](https://exeter-omics-servers.github.io/omics-server-documentation/).

## Building and hosting these pages locally

In the event that the pages are down, you have no internet connection or you
are editing the documentation, follow the code block below to build and serve
the documentation locally.

```sh
# Assumes some kind of conda binary is installed and configured on your system.
# Go to https://github.com/conda-forge/miniforge if you do not have this.

cd .../omics-server-documentation/envs/ # envs/ directory in this repo

conda env create
conda activate omics_server_docs

cd .../omics-server-documentation/ # root of this repository
mkdocs serve
```

After completing this you will be met with something like this:

```text
INFO    -  Building documentation...
INFO    -  Cleaning site directory
INFO    -  Documentation built in 1.96 seconds
INFO    -  [00:00:00] Watching paths for changes: 'docs', 'mkdocs.yml'
INFO    -  [00:00:00] Serving on http://127.0.0.1:8000/
```

Going to the link provided in this text will send you to the documentation.

> [!NOTE]
> Your port may be different to `8000` depending on your machine and which
> ports are already in use.

## Contributing

If anything in the documentation is worded poorly, outdated, includes typos,
requires more context (*etc.*) please open an issue on this repository (you
will need a GitHub account for this).

> [!WARNING]
> Do not add any sensitive information to these docs. These pages need to be
> helpful to users and admins without creating a roadmap for potential
> attackers. 
>
> Examples of what not to include might be:
>
> - Credentials
> - Network information
> - Detailed security configurations
> - Detailed software versions
> - Information on scripts that handle sensitive tasks
> - Admin connections methods
