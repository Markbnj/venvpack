# venvpack

venvpack is a bash shell script which packages and unpackages python
virtualenvs and related projects into a single transportable (within
limits!) file.

## Releases

##### [0.1.0-beta](https://github.com/Markbnj/venvpack/releases/tag/v0.1.0-beta)
  * 3/15/2016, initial release.

## Documentation

### Table of Contents
* [Why](#why)
* [Pre-release](#pre-release)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Use](#use)
* [Relocation and --relocatable](#relocation-and---relocatable)
* [Architecture compatibility](#architecture-compatibility)
* [Options](#options)

### Why?

The venvpack script arose from my work on [venv2docker](https://github.com/Markbnj/venv2docker).
In order to get a python virtualenv into a docker image I needed to figure out how to move it
from one file system to another, and that led me to think about this more general use. In fact
now that this script is working the docker script could just invoke it to do the work of
packaging and unpackaging the environment, and I may have it do that at some point.

Is this useful? We'll see. I can imagine some uses cases. For example: you have a lot of large
venvs and want to archive a few of them away; you want to email a venv to someone or put it
into a cloud bucket to share.

### Pre-release

venvpack is early. It has been tested on ubuntu 15.10 only, and against a very limited number of
virtualenvs using python 2.7+. All the same caveats mentioned in the venv2docker readme apply.
I would like as much feedback as possible so if you do use the script and have any problems
please open an issue here so I can follow up.

Having said that, there is not much risk in your trying this script for the
following reasons:

1. The script does not write to an existing virtualenv.
2. The script does not write to any existing project directory.
3. It cleans up after itself.

### Prerequisites

venvpack needs a working python and [virtualenv](https://virtualenv.readthedocs.org/en/latest/)
installation, of course.

The script requires tar, gzip, grep, sed, and getopt.

### Installation

Either grab the latest release archive from the links at the top, or clone the repository
to get latest. You can either run the script right in the bin directory or put it on the path.
The script is standalone and you can copy it anywhere you like.

### Use

```
venvpack [OPTION]... [TARGET]

Package a python virtualenv into a single transportable file that
can be moved between file systems.

In the normal mode of operation the script packages a virtualenv into
a single file, and TARGET refers to the virtual env name. If TARGET is
not supplied the current active virtualenv, if any, is packaged.

If the -o|--outfile option is not supplied the output file will be
given the same name as the source virtualenv.

When the -r|--restore option is used to restore a virtualenv TARGET
is the path and name of the .venv file, and is required.

Example:
    venvpack testenv

Packages the testenv virtualenv into 'testenv.venv' in the current
working directory.

Example:
    venvpack --restore testenv.venv

Restores the packaged virtualenv in testenv.venv into the local
filesystem.

Options:
    -c, --no-cleanup-on-error   Do not remove temporary files on error.
    -d, --debug                 Print diagnostic information after packaging.
    -f, --force                 Force restoring even if architecture check fails.
    -o, --outfile=PATH          Specify the output file name and path.
    -r, --restore               Restore a packaged virtualenv.
    -y, --no-prompt             Do not prompt for confirmation before building.
```

### Relocation and --relocatable

The same caveats apply as discussed in the [venv2docker](https://github.com/Markbnj/venv2docker/blob/master/README.md#relocation-and---relocatable)
readme, so please see that document for more information.

### Architecture compatibility

As with venv2docker you cannot depend on being able to move a virtualenv
between 32-bit and 64-bit platforms, for example. Other architecture
incompatibilities can arise. When you restore the script will attempt to
check that at least python is where the virtualenv expects it to be. If
it is not the script will exit with an error unless the -f option is
applied on the command line.

### Options

The following command line parameters control various aspects of the final image
produced by venv2docker.

----

###### --no-cleanup-on-error

Example:

`venvpack --no-cleanup-on-error my_test_env`

The normal behavior of venvpack is to clean up after itself as much as it
can. If an error occurs the script will remove temporary files created up to
the point where the error occurred.

In the case of the --restore flag the script will also attempt to clean up
any project or virtualenv files that have already been restored.

Sometimes retaining these files may help solve an error. Use this argument to
prevent removing these files.

----

###### -d|--debug

Example:

`venvpack --debug my_test_env`

Does nothing of any use at this time.

----

###### -f|--force

Example:

`venvpack --restore --force my_test_env`

If venvpack detects that the system python installation is not where a restored
venv would expect it to be it will abort restoring the venv and clean up after
itself. Use this argument to force the script to continue restoring the venv.

----

###### -o|--outfile

Example:

`venvpack -o /home/me/venvs/mytest.venv my_test_env`

When archiving specifies the location and name of the output archive file. By
default this file will be written to the current working dir as venv_name.venv.

`venvpack --restore --force my_test_env`

When restoring specifies the path to the base folder for restoring the project
files. By default the script will try to use the PROJECT_HOME var in order to
figure out where to recreate a project.

----

###### -r|--restore

Example:

`venvpack --restore mytest.venv`

Restores an archived virtualenv to the local filesystem. The operation will
fail if the project or virtualenv already exist, or if the system python
install is not where the venv expects it and the --force flag was not used.

----

###### -y|--no-prompt

Example:

`venvpack -y --restore mytest.venv`

Suppress the normal confirmation prompt that is displayed before a venv is
archived or restored.

