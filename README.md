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
`(node)$> module avail`  
Note that this would output a lot of text on the clusters since there are a lot of installed software, to limit the output we can limit it to what we are interested in, for example the GROMACS software :

    ```
    (node)$> module avail 2>&1 | grep -i gromacs
    ```
This will only output the software from the software stack that contain "gromacs" in their name.

## Adding a software to the existing stack

## Replicating the architecture of the platform on a local environment