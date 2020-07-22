Introduction
############

Installing the Calm DSL
.......................

Before using the Nutanix Calm DSL, we'll need to make sure it is available for use on our development workstation.  Let's do that now.

Preparing Ubuntu Linux for Docker
.................................

Once you have your Ubuntu Server 20.04 VM or machine running, it's important to make sure the server has the latest updates.  These typically cover security and bug fixes and are a good idea on any server, regardless of the operating system in use.

Installing Updates + OpenSSH Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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

Installing Docker
~~~~~~~~~~~~~~~~~

#. We can now install the prerequisites for running the Docker container.  Please note these specific steps are taken directly from the `Install Docker Engine on Ubuntu <https://docs.docker.com/engine/install/ubuntu/>`_ page.

   .. code-block:: bash

      sudo apt-get remove docker docker-engine docker.io containerd runc
      sudo apt-get update && sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      sudo apt-key fingerprint 0EBFCD88
      sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      sudo apt-get update
      sudo apt-get install docker-ce docker-ce-cli containerd.io
      sudo docker run hello-world

   At the completion of these steps, you will receive a message informing you that Docker has been installed and can be used to run the `hello-world` demo container.

   .. figure:: images/docker_run_hello_world.png

Summary
.......

In this quick intro we completed the following steps:

- Ensured our Ubuntu machine is running the latest updates
- Installed OpenSSH-Server and verified we can login
- Installed Docker in preparation for running the Calm DSL Docker container
