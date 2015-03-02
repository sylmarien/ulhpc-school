# UL HPC Tutorial: Use and manage software with module and RESIF on the UL HPC platform

The objective of this tutorial is to present how to interact with the software installed on the UL HPC platform, from using provided software to extending the stack by adding new software on the platform, and also replicating the architecture on a local machine.

This course is divided in three chapters that are going through each of the previously stated use cases.

The [first chapter](#understanding-and-using-the-software-available-on-the-ul-hpc-platform) goes through the architecture of the software stack and the basic tools to start using them.  
The [second chapter](#adding-a-software-to-the-existing-stack) goes through the process of adding new software to the existing stack as a user using RESIF, an internally developped tool to manage the software stack on the platform.  
The [third chapter](#replicating-the-architecture-of-the-platform-on-a-local-environment) goes through the process of replicating the software stack present on the platform, or part of it, on a local environment.

## Understanding and using the software available on the UL HPC platform

Before starting this tutorial, please make sure you are on a compute node and not on the access node. To get ressources on a compute, use the following command:  
`(access)$> oarsub -I -l nodes=1,walltime=1:00:00`  
(for more details about this command and the node reservation process on the clusters, please referer to the [ULHPC documentation](https://hpc.uni.lu/users/docs/oar.html).)

The software architecture on the platform revolves around the `module` tool. This command is at the core of the workflow to use a software on the platform, so we're going to cover its most basic command before going any further.

### `module` command basics and basic workflow

`module` can list all the software modules installed in the software stack:  

    (node)$> module avail
    ------------------- /opt/apps/devel/v0.0-20150212/core/modules/bio ----------------
    bio/ABySS/1.3.4-goolf-1.4.10-Python-2.7.3        bio/Bowtie2/2.2.2-goolf-1.4.10   (D)
    bio/ABySS/1.3.4-ictce-5.3.0-Python-2.7.3  (D)    bio/Cufflinks/2.0.2-goolf-1.4.10
    [...]
Note that this would output a lot of text on the clusters since there are a lot of installed software, to limit the output we can limit it to what we are interested in, for example the GROMACS software :
    
    (node)$> module avail gromacs
    ----------- /opt/apps/devel/v0.0-20150212/core/modules/bio --------------
    bio/GROMACS/4.6.1-ictce-5.3.0-hybrid    bio/GROMACS/4.6.1-ictce-5.3.0-mt
    bio/GROMACS/4.6.5-goolf-1.4.10-mt (D)
    [...]
This will only output the software modules from the software stack that contain "gromacs" in their name.

To start using the version you want, for example `bio/GROMACS/4.6.5-goolf-1.4.10-mt`, we are going to `load` the software module:  
`(node)$> module load bio/GROMACS/4.6.5-goolf-1.4.10-mt`

You can now use the software and work with it. To check that it is actually loaded, list the loaded software modules:

    (node)$> module list
    Currently Loaded Modules:
        1) compiler/GCC/4.7.2             4) toolchain/gompi/1.4.10                            7) numlib/ScaLAPACK/2.0.2-gompi-1.4.10-OpenBLAS-0.2.6-LAPACK-3.4.2
        2) system/hwloc/1.6.2-GCC-4.7.2   5) numlib/OpenBLAS/0.2.6-gompi-1.4.10-LAPACK-3.4.2   8) toolchain/goolf/1.4.10
        3) mpi/OpenMPI/1.6.4-GCC-4.7.2    6) numlib/FFTW/3.3.3-gompi-1.4.10                    9) bio/GROMACS/4.6.5-goolf-1.4.10-mt

When you're finished working with it, unload the software module:  
`(node)$> module unload bio/GROMACS/4.6.5-goolf-1.4.10-mt`

However, this will only unload the `bio/GROMACS/4.6.5-goolf-1.4.10-mt` software module itself, not its dependencies, as you can see it:

    (node)$> module list
    Currently Loaded Modules:
        1) compiler/GCC/4.7.2             4) toolchain/gompi/1.4.10                            7) numlib/ScaLAPACK/2.0.2-gompi-1.4.10-OpenBLAS-0.2.6-LAPACK-3.4.2
        2) system/hwloc/1.6.2-GCC-4.7.2   5) numlib/OpenBLAS/0.2.6-gompi-1.4.10-LAPACK-3.4.2   8) toolchain/goolf/1.4.10
        3) mpi/OpenMPI/1.6.4-GCC-4.7.2    6) numlib/FFTW/3.3.3-gompi-1.4.10
You could unload all these dependencies by hand, but it would be too long and painful, the efficient way is to `purge` the loaded software modules to restore the initial state of the session:  
`(node)$> module purge`  
This unloads *all* the software modules that you see with `module list`.

Now that we have the basics to manipulate the software modules, we're going to deeper look at them and the different concepts surrounding them.

### Software stack architecture

The upper layer of the architecture is what we call the *sofwtare set*. It is a collection of software, common example are a _core_ software set that would contain only tested software and an _experimental_ one that would contain untested software.  
The goal of these groupings is to provide information on the degree of support for the various software.

Inside of these software sets, software are named with regard to a *naming scheme* which provides information on the software and allows for a better presentation of results of the `module avail` command.  
Software named following following this naming scheme have the following skeleton: **software_class/software_name/software_complete_version** where  
- software_class describes the category in which the software is classified and can be found among the following classe: [base, bio, cae, chem, compiler, data, debugger, devel, lang, lib, math, mpi, numlib, phys, system, toolchain, tools, vis]
- software_name is the name of the software, e.g. GROMACS or ABySS
- software_complete_version is the complete version of the software: it contains the version of the software itself followed by the type and version of the main dependencies it relies on (e.g. compiler) with the following format: software_version-dependencies_versions

What all this architecture allows when considering the usage of the software stack is that it provides more information in a more structured way.  
Firstly, all the software of a given software set will be grouped together when listing the software with the `module avail` command.  
And inside of a given software set, the various software will be groupe by software class.

The `module avail` command will then have the following general output:  
![module avail output](https://cloud.githubusercontent.com/assets/8776275/6438964/53d480e6-c0ce-11e4-815c-6c5bc075bb1d.png)

## Add a software to the existing stack

In this part, we are going to go through the steps to add a software that is not already provided on the platform. This is done using the RESIF tool.
So first of all we are going to install this software and then use it to add a software (bzip2).

We are still going to work from a job (node) here.

### Installation of RESIF

First, there are a few prerequisites that we need to have:

- Python 2.6 or above
- pip (a python command line installer)
- git

Once all of that is installed, we can start installing RESIF.

First, we install a Python dependency:

        (node)$> pip install --install-option="--prefix=$HOME/.local" vsc-base

We can now install RESIF itself:

        (node)$> pip install --install-option="--prefix=$HOME/.local" resif

To make it available and working, we need to modify some environement variables:

        (node)$> export PATH=$PATH:~/local/bin
        (node)$> export PYTHONPATH=$PYTHONPATH:~/local/lib/python2.7/site-packages

And then initialize it (this will download the required sources to build new software):

        (node)$> resif init

Once this is finished we can start the steps to actually install the new software.

### Installation of additional software

First we need to create a file that describes what we want to install. Create a file (we are assuming it is in your home directory), name it `swsets.yaml` and make its content to match the following lines:

        mysoftware:
          - bzip2-1.0.6.eb

This is the format that RESIF reads, the layout is the following:

        software_set1:
          - software1
          - software2
        software_set2:
          - software3
You can put as much software and/or software sets as you want.

Now install the software using the build subcommand:

        (node)$> resif build --installdir ~/.local/resif --swsets-config ~/swsets.yaml mysoftware
This will install the software using ~/.local/resif as the root of the installation.

Tom ake the software available yo uthen need to add its path to the list of the available pathes:

        (node)$> export MODULEPATH=~/.local/resif/mysoftware/modules/all:$MODULEPATH

Now, we should see `bzip2` at the very beginning of the output of the list of the software modules:

        (node)$> module avail
        ----- /home/users/username/.local/resif/mysoftware/core/modules/all -----
        tools/bzip2/1.0.6

Your software is installed and ready to use.

RESIF offers a lot more possibilities than what we just saw, for more details, go check the [documentation](LINK_TO_ADD_HERE) of the tool.

## Replicating the architecture of the platform on a local environment

In this part, we are going to create a software stack from scratch. This is especially useful if you want to work on a local machine (e.g. your laptop) with the same tools than those provided on the platform (for example when you're travelling).

We suppose that RESIF is already installed, if not, follow the instructions described [above](#installation-of-resif).

### Direct method

The first thing to do is to create the swsets.yaml file that describes which software we want to install. Create in your home directory a file named `swsets.yaml` and make it match the following content:

        core:
          - bzip2-1.0.6.eb
          - ABINIT-7.2.1-x86_64_linux_gnu4.5.eb

We could make the list go longer, but this would take much longer and is not necessary for our example.

We can now install all the software stack in a single command:

        $> resif cleaninstall --swsets-config ~/swsets.yaml core
This will install everything using `~/.local/resif` as the root of the installation.

To use this newly installed software stack, source the LOADME file inside of the rootinstall directory (This path should look like this: `~/.local/resif/devel/vx.y-YYYYMMDD/LOADME-vx.y-YYYYMMDD.sh`)

The software stack is now ready to be used.

### Indirect method

In this method we are going to create the architecture and then add software to it.

We create the architecture using the `bootstrap` subcommand:

        $> resif bootstrap
This will create the architecture using `~/.local/resif` as the root of the installation.

We now need to make this architecture active: source the LOADME file inside of the rootinstall directory (This path should look like this: `~/.local/resif/devel/vx.y-YYYYMMDD/LOADME-vx.y-YYYYMMDD.sh`)

Then we need to create the file that describe the software we want to install. In your home directory, create a file named `swsets.yaml` and make it match the following content:

        core:
          - bzip2-1.0.6.eb
          - ABINIT-7.2.1-x86_64_linux_gnu4.5.eb

We could make the list go longer, but this would take much longer and is not necessary for our example.

We now only need to build the given software:

        $> resif build --swsets-config ~/swsets.yaml core

This will install the software listed in the swsets.yaml file. The software stack is now ready to be used.


To learn more about RESIF and how to control more parameters of its usage, please refer to the [documentation](LINK_TO_ADD_HERE) of the tool.

To conclude this tutorial, here is a schema that summarizes the previous parts:

![General Workflow](https://cloud.githubusercontent.com/assets/8776275/6438824/177e4042-c0cd-11e4-8693-85a27c11eff5.png)