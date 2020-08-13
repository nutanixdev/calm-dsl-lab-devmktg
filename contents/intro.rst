Introduction
############

Installing the Calm DSL
.......................

Before using the Nutanix Calm DSL, we'll need to make sure it is available for use on our development workstation.  Let's do that now.

Preparing Ubuntu Linux for Docker
.................................

Once you have your Ubuntu Server 20.04 VM or machine running, it's important to make sure the server has the latest updates.  These typically cover security and bug fixes and are a good idea on any server, regardless of the operating system in use.

Installing Updates + Packages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To update your Ubuntu Server, please complete the following steps before continuing.

#. If your server does *not* have an SSH server installed, please install and enable OpenSSH-Server as follows.

   .. code-block:: bash

      sudo apt-get -y update && sudo apt-get -y upgrade
      sudo apt-get install -y openssh-server
      sudo systemctl start ssh && sudo systemctl enable ssh

#. Verify you can login to your SSH server either locally or via SSH.  If you are using SSH (recommended), proceed below.

   .. code-block:: bash

      ssh <username>@<ip_address>

   If you don't know the IP address of your Ubuntu machine e.g. if running a VM, you can use the following command to verify.  The `ifconfig` command can also be used, but may not be installed by default.

   .. code-block:: bash

      ip addr

   An example of running `ip addr` is shown below.  Please be aware it is possible your configuration will be different for both interface name (enp0s3) and IP address (10.0.2.15).

   .. figure:: images/ip_addr_example.png

#. Verify `git` is installed.  This is required for downloading the Calm DSL in an upcoming step.

   .. code-block:: bash

      sudo apt-get install -y git

Installing Docker
~~~~~~~~~~~~~~~~~

#. We can now install the prerequisites for running the Docker container.  Please note these specific steps are taken directly from the `Install Docker Engine on Ubuntu <https://docs.docker.com/engine/install/ubuntu/>`_ page.

   .. code-block:: bash

      sudo apt-get remove docker docker-engine docker.io containerd runc
      sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      sudo apt-key fingerprint 0EBFCD88
      sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      sudo apt-get update
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io
      sudo docker run hello-world

   At the completion of these steps, you will receive a message informing you that Docker has been installed and can be used to run the `hello-world` demo container.

   .. figure:: images/docker_run_hello_world.png

Enabling Docker & Setting Permissions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The final step, just as a precaution, is to ensure Docker starts when or if the system gets rebooted.

#. Run the following command on your Ubuntu Linux machine.

   .. code-block:: bash

      sudo systemctl start docker && sudo systemctl enable docker

   This will ensure Docker is always available within an SSH or terminal session on your Ubuntu machine.

#. Lastly, set the permissions for our user so we don't need to run Docker with `sudo` every time.

   .. code-block:: bash

      sudo usermod -aG docker $USER

Summary
.......

In this quick intro we completed the following steps:

- Ensured our Ubuntu machine is running the latest updates
- Installed OpenSSH-Server and verified we can login
- Installed Docker in preparation for running the Calm DSL Docker container
- Configured our user account so it can run Docker without `sudo` privilege escalation
