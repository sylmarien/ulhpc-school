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
`oarsub -I -l nodes=1,walltime=4`  
(for more details about this command and the node reservation process on the clusters, please referer to the [ULHPC documentation](https://hpc.uni.lu/users/docs/oar.html).)

The software architecture on the platform revolves aroung the `module` tool. This command is at the core of all the workflow of using a software on the platform.

## Adding a software to the existing stack

## Replicating the architecture of the platform on a local environment