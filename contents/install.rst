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

Preparing to build the Container
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In order to build the Calm DSL container, a few Linux package prerequisites must be satisfied.  We can easily install those via `apt`, as follows.

#. Install the package prerequisite packages.

   .. code-block:: bash

      sudo apt-get install -y git python3 python3-venv openssl build-essential make libssl-dev python3-pip

   This process can take a few minutes depending on the speed of your internet connection.  Please wait for this process to finish and ensure no errors occurred during the package install process.  The last few lines of this process are shown below.

   .. figure:: images/calm_dsl_prerequisites.png

   .. figure:: images/pip3_install_wheel.png

Building the Calm DSL Container
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

With our system now configured to build the Calm DSL container, we can do exactly that.  For this process to function as expected, we'll also make sure these steps are completed within a Python virtual environment.

#. Build the container as follows.

   .. code-block:: bash

      cd ~/nutanix/calm-dsl
      python3 -m venv venv
      . venv/bin/activate
      pip3 install wheel
      make docker

Run the Container
~~~~~~~~~~~~~~~~~

The great thing about proceeding this way is that we now have a nicely 'packaged' environment for using the Calm DSL.  Our prerequisites are installed, the container is built and ready for use - the only thing left to do now is run the container itself.

#. Run the container as follows.

   .. code-block:: bash

      make run

   .. figure:: images/make_run.png

   The Nutanix Calm DSL Docker is now running.

Summary
.......

In this setup section we completed the following steps:

- Downloaded the Nutanix Calm DSL files by cloning the Calm DSL public GitHub repository
- Install Linux package prerequisites for building the Calm DSL Docker container
- Built and ran the Calm DSL Docker container

In the next section we'll configure this specific Docker container instance to connect to Prism Central and talk to Calm.
