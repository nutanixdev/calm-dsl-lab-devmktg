Installing the Calm DSL Container
#################################

Installing the Calm DSL
.......................

Before continuing, please note the Nutanix Calm DSL can be run outside of a Docker container, if you choose.  This lab covers using the DSL within a Docker container only.  The actual DSL usage steps are identical regardless of which method is used.  If you prefer, you may choose to run the DSL on 'native' Linux without requiring Docker - please see `Introducing the Nutanix Calm DSL <https://www.nutanix.dev/2020/03/17/introducing-the-nutanix-calm-dsl/>`_ if you would like additional information.

Downloading the Calm DSL
~~~~~~~~~~~~~~~~~~~~~~~~

Once you have your Ubuntu Server 20.04 VM or machine running, it's important to make sure the server has the latest updates.  These typically cover security and bug fixes and are a good idea on any server, regardless of the operating system in use.

Along the way, we will keep our source code and files clean by creating a directory for today's lab.

#. Create a directory for the Calm DSL files.

   .. code-block:: bash

      mkdir -p ~/nutanix
      cd ~/nutanix

#. Clone the Calm DSL files from the public `Calm DSL GitHub repository <https://github.com/nutanix/calm-dsl>`_.

   .. code-block:: bash

      git clone https://github.com/nutanix/calm-dsl.git
      ls -la

   We can verify the Calm DSL repository has been cloned by simply checking the `git` command results and also that we now have a new directory named `calm-dsl`.

   .. figure:: images/clone_dsl_files.png

Building or running Calm DSL Container
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The Nutanix Calm DSL is aimed at automation engineers, scripting teams or developers that need to control Nutanix Calm via the command line.  However, there are two ways of using the Nutanix Calm DSL container.

1. The same way as any other publicly-available container via `docker run`.  This is the easiest method and is recommended if you are not going to be actively working on the Calm DSL codebase.
2. Compiling the container locally so that any changes you make are built into your own local version of the container.  This is recommended for advanced users.

This lab will cover both methods, but it is strongly recommended to use method #1 unless local container development is a requirement.

**Method 1 - `docker run`** [Recommended for most users]

#. On your laptop/PC with Docker installed, run the following command.

   .. code-block:: bash

      docker run -it ntnx/calm-dsl

   That's it!  In all honesty this doesn't even need to be a method on its own, but demonstrates how easy it is to use the Calm DSL container that has been published by the Calm engineering team.

**Method 2 - Building the container** [Recommended for advanced users only]

In order to build the Calm DSL container, a few Linux package prerequisites must be satisfied.  We can easily install those via `apt`, as follows.

#. Install the prerequisite packages.

   .. code-block:: bash

      sudo apt-get install -y git python3 python3-venv openssl build-essential make libssl-dev python3-pip

   This process can take a few minutes depending on the speed of your internet connection and on the resources available to your Linux machine.  Please wait for this process to finish and ensure no errors occurred during the package install process.  The last few lines of this process running successfully are shown below.

   .. figure:: images/calm_dsl_prerequisites.png

   .. figure:: images/pip3_install_wheel.png

   With our system now configured to build the Calm DSL container, we can do exactly that.  For this process to function as expected, we'll also make sure these steps are completed within a Python virtual environment.

#. Build the container as follows.

   .. code-block:: bash

      cd ~/nutanix/calm-dsl
      python3 -m venv venv
      . venv/bin/activate
      pip3 install wheel
      make docker

#.  Run the container as follows.

    .. code-block:: bash

       make run

    .. figure:: images/make_run.png

    The Nutanix Calm DSL Docker is now running.

Summary
.......

In this setup section we completed the following steps:

- Downloaded the Nutanix Calm DSL files by cloning the Calm DSL public GitHub repository
- [Option 1] Ran the `ntnx/calm-dsl` container directly using `docker run`
- [Option 2] Installed Linux package prerequisites, then built and ran the container

In the next section we'll configure this specific Docker container instance to connect to Prism Central and talk to Calm.
