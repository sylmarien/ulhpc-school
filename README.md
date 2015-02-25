`README.md`

Copyright (c) 2015 Maxime Schmitt <maxime.schmitt@telecom-bretagne.eu>

-------------------

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

`module` can list all the software installed in the software stack:  

    (node)$> module avail
    --------------------------- /opt/apps/HPCBIOS/modules/bio -------------------------
    ABySS/1.3.4-goolf-1.4.10-Python-2.7.3          FASTX-Toolkit/0.0.13.2-goolf-1.4.10
    ABySS/1.3.4-ictce-5.3.0-Python-2.7.3           FASTX-Toolkit/0.0.13.2-ictce-5.3.0
    [...]
Note that this would output a lot of text on the clusters since there are a lot of installed software, to limit the output we can limit it to what we are interested in, for example the GROMACS software :

    
    (node)$> module avail gromacs
    GROMACS/4.6.1-goolf-1.4.10-hybrid
    GROMACS/4.6.1-goolf-1.4.10-mt
    GROMACS/4.6.1-goolfc-1.3.12-hybrid
    GROMACS/4.6.1-goolfc-1.3.12-mt
    [...]
This will only output the software from the software stack that contain "gromacs" in their name.

To start using the version you want, for example `GROMACS/4.6.1-goolf-1.4.10-mt`, we are going to `load` the software:  
`(node)$> module load GROMACS/4.6.1-goolf-1.4.10-mt`

You can now use the software and work with it. To check that it is actually loaded, list the loaded software:

    (node)$> module list
    Currently Loaded Modulefiles:
        1) GCC/4.7.2                4) gompi/1.4.10                              7) ScaLAPACK/2.0.2-gompi-1.4.10-OpenBLAS-0.2.6-LAPACK-3.4.2
        2) hwloc/1.6.2-GCC-4.7.2    5) OpenBLAS/0.2.6-gompi-1.4.10-LAPACK-3.4.2  8) goolf/1.4.10
        3) OpenMPI/1.6.4-GCC-4.7.2  6) FFTW/3.3.3-gompi-1.4.10                   9) GROMACS/4.6.1-goolf-1.4.10-mt

When you're finished working with it, unload the software:  
`(node)$> module unload GROMACS/4.6.1-goolf-1.4.10-mt`

However, this will only unload the `GROMACS/4.6.1-goolf-1.4.10-mt` software itself, not its dependencies, as you can see it:

    (node)$> module list
    Currently Loaded Modulefiles:
        1) GCC/4.7.2                4) gompi/1.4.10                              7) ScaLAPACK/2.0.2-gompi-1.4.10-OpenBLAS-0.2.6-LAPACK-3.4.2
        2) hwloc/1.6.2-GCC-4.7.2    5) OpenBLAS/0.2.6-gompi-1.4.10-LAPACK-3.4.2  8) goolf/1.4.10
        3) OpenMPI/1.6.4-GCC-4.7.2  6) FFTW/3.3.3-gompi-1.4.10
You could unload all these dependencies by hand, but it would be too long and painful, the efficient way is to `purge` the loaded software to restore the initial state of the session:  
`(node)$> module purge`  
This unloads *all* the software that you see with `module list`.

## Add a software to the existing stack

## Replicating the architecture of the platform on a local environment