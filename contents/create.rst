Creating Blueprint
##################

So far we've looked at many aspects of the Calm DSL:

- Installation
- Creating a sample blueprint
- Uploading and launching the sample blueprint
- Getting help

They are critical topics when applied to working with the Calm DSL, but what about using the Calm DSL "for real" and creating our own Calm DSL blueprint from scratch?

Preparation
...........

To start this process, please ensure your Ubuntu VM is running, the Calm DSL container is running and that you have the ability to run Calm DSL commands within the container.  If your container has only just started, please run `calm init dsl` to connect the Calm DSL to Prism Central.

Directory and File Structure
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Within the container, create directory named `BasicLinuxVM`.

   .. code-block:: bash

      mkdir -p ~/nutanix/BasicLinuxVM

#. Create the directory structure for the blueprint; we'll go over each directory shortly.

   .. code-block:: bash

      mkdir -p ~/nutanix/BasicLinuxVM/.local
      mkdir -p ~/nutanix/BasicLinuxVM/scripts
      mkdir -p ~/nutanix/BasicLinuxVM/specs

   The directories above are as follows.

   - **.local/** - Used to store blueprint-specific credentials, e.g. SSH key pairs
   - **scripts/** - Used to store scripts that run as part of application actions, from deploying to post-deployment
   - **specs/** - YAML data that defines various specifications for the application's single Linux VM

#. Create empty files that will be populated with our application definitions:

   .. code-block:: bash

      touch ~/nutanix/BasicLinuxVM/.local/superuser_private_key
      touch ~/nutanix/BasicLinuxVM/.local/superuser_public_key
      touch ~/nutanix/BasicLinuxVM/specs/basiclinuxvm_editables.yaml
      touch ~/nutanix/BasicLinuxVM/specs/basiclinuxvm_cloudinit_data.yaml
      touch ~/nutanix/BasicLinuxVM/scripts/package_install_configure_base_vm.sh
      touch ~/nutanix/BasicLinuxVM/scripts/package_install_configure_firewall.sh
      touch ~/nutanix/BasicLinuxVM/blueprint.py

   The files above are as follows.

   - **.local/superuser_private_key** - Will store a copy of our SSH key pair's private key
   - **.local/superuser_public_key** - Will store a copy of the SSH key pair's public key
   - **specs/basiclinuxvm_editables.yaml** - Editable spec definitions for, by default, VM NICs and serial ports
   - **specs/basiclinuxvm_cloudinit_data.yaml** - Cloud-Init data that will be used to configure the VM after it is created
   - **scripts/package_install_configure_base_vm.sh** - A shell script that will update the VM's Linux packages and carry out some other basic tasks
   - **scripts/package_install_configure_firewall.sh** - A shell script that will install and configure `firewalld`, a common Linux firewall package
   - **blueprint.py** - The blueprint itself that ties all the above files together into a blueprint definition

SSH Key Pair
~~~~~~~~~~~~

To save time, this lab will use an SSH key pair that was generated previously.

#. Edit **.local/superuser_private_key** so that it contains the following:

   .. code-block:: bash

      -----BEGIN RSA PRIVATE KEY-----
      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
      -----END RSA PRIVATE KEY-----

   .. note::

      It is important to ensure the file contents appear exactly as shown above, including the `-----BEGIN RSA PRIVATE KEY-----` and `-----END RSA PRIVATE KEY-----` lines.

#. Edit **.local/superuser_public_key** so that it contains the following:

   .. code-block:: bash

      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCm+7N2tjmJw5jhPmD8MS6urZQJB42ABh73ffGQSJ0XUHgdEDfjUDFkLK0wyJCe0sF5QJnh07UQn0F0BUnBi+VwehPGeODh6S43OP5YS/14L0fyntFI06B9lckx/ygRNu82sHxXCX+6VVUFPOPC+sz6j1DQswKY9d4cEYnaMBGSzqRxrqAIf6aWIKTJTYKPFY0zaUZ6ow2iwS0Nlh5EqaXsEBWkqMmr7/auP9GV/adUgzFrGLJklYBdfH575SIK6/PZL6wNT0jE9LmFlEm7dI01ZWPclBuV16FzRyrnzmWr/ebY62A04vYBtR0vyfEfsW2ZgxgD6aAE6+ytj0v19y0elRtOaeTySN/HlXh7owKWCHnlXNpTUiSDP8SQ8LRARkhQu3KEDL0ppGCrSF87oFkp1gPzf92U+UK3LaNMMjZXMOy0zLoLEdLtbQo6S8iHggDoX4NI4sWWxcX0mtadvjy/nIOvskk9IXasQh0u0MT9ARQY5VXPluKDtEVdeow9UbvgJ1xxNkphUgsWjCiy+sjgapsuZvWqKM6TPT1i24XYaau+/Fa0vhjLb8vCMWrrtkRwGt4re243NDYcYWTzVZUFuUK0w1wqt77KgjCCeyJdsZNwrh15v780Fjqpec3EGVA0xyNbF0jn/tsnYy9jPh/6Cv767EratI97JhUxoB4gXw== no-reply@acme.com

   .. note::

      The Nutanix Calm DSL will look for local files in a specific locations. Your lab blueprint is located in `~/nutanix/BasicLinuxVM`.  We’ll get to this shortly, but there’s a Calm DSL statement called `read_local_file` that will look in the following directories, in this order:

      - Blueprint root, in a folder named .local/ (your lab blueprint's .local folder would end up being ~/nutanix/BasicLinuxVM/.local)
      - In ~/.calm/.local/

      This means that if you do end up storing your credentials in the .local/ folder within the blueprint structure, it’s possible you could accidentally push those to source control.  Don’t do that.  In production, it would be strongly preferable to store SSH keys outside the blueprint structure, within `~/.calm/.local`.

Spec Editables
~~~~~~~~~~~~~~

#. Edit the **specs/basiclinuxvm_editables.yaml** file so that it contains the following:

   .. code-block:: yaml

      resources:
        nic_list: {}
        serial_port_list: {}

   We actually don't need to specify any NIC or serial port information in this simple blueprint, so can leave those sections empty.  However, if we were to specify NIC information using the editable spec file like this, an example would be as follows (don't use this in the lab - it's just an example):

   .. code-block:: yaml

      nic_list:
      - ip_endpoint_list: []
        network_function_nic_type: INGRESS
        nic_type: NORMAL_NIC
        subnet_reference:
          kind: subnet
          uuid: 00000000-0000-0000-0000-000000000000

#. Edit the **specs/basiclinuxvm_cloudinit_data.yaml** file so that it contains the following.  Ensure indentation is exactly as shown below.

   .. code-block:: yaml

      #cloud-config
      users:
        - name: @@{SuperUser.username}@@
          ssh-authorized-keys:
            - @@{InstancePublicKey}@@
            - @@{SuperUser.public_key}@@
          sudo: ['ALL=(ALL) NOPASSWD:ALL']

   What is happening here?  The CentOS Linux image that our VM will be based on shortly has been preconfigured to contain the `Cloud-Init` package.  Cloud-Init is a popular and common way of customising a deployed VM the first time it is powered on, without any input from the user.  In our case, we are passed the Cloud-Init data above to the VM as it is created.  Here is what the Cloud-Init data will do:

   - Create a user that matches the username for our blueprint's main credential.  This credential is called `SuperUser` and will be created shortly.  For reference, the username within that credential is `centos`.
   - Get the value of a blueprint variable named `InstancePublicKey` and add it as an SSH authorized key for the new `centos` user.
   - Get the SSH public key from our SSH private key and add it as an SSH authorized key for the new `centos` user.
   - Configure the `centos` user so it can act as a password-less `sudo` administrator.

Package Install Scripts
~~~~~~~~~~~~~~~~~~~~~~~

The next steps are to create the scripts that run at various stages during the application lifecycle.  This particular application is made up of a single VM that runs two scripts.  The first script updates CentOS with the latest packages, and the second script installs and configures a basic firewall.  Let's create those now.

#. Edit the **scripts/package_install_configure_base_vm.sh** script so that it contains the following.

   .. code-block:: bash

      #!/bin/bash

      # update base OS packages
      sudo yum -y update
      sudo yum -y upgrade

      # install some useful packages
      sudo yum -y install vim git

   This script, as mentioned earlier, updated the CentOS Linux system packages, then installs the `vim` and `git` packages.  We won't explicitly use those in today's lab, but it shows how easily a package install script can do anything we choose when it runs.

#. Edit the **scripts/package_install_configure_firewall.sh** script so that it contains the following.

   .. code-block:: bash

      #!/bin/bash

      # install and enable firewalld
      sudo yum -y install firewalld
      sudo systemctl enable firewalld
      sudo systemctl start firewalld

   This script, as mentioned earlier, installs the `firewalld` package, starts the `firewalld` service then ensures the `firewalld` service is **enabled** i.e. runs during boot.

Create the Blueprint
....................

Now that our blueprint's supporting files are created and in place, we can create `blueprint.py`.  This file is the blueprint itself, and ties all the supporting files together using native Python 3 code.

Before creating the **blueprint.py** file, some environment-specific information is required.  Complete the following sections and note down each configuration setting as it pertains to your Prism Central instance.

#. From within Prism Central, obtain the name of your Prism Element cluster.  On the default Dashboard with a single Prism Element registered, this is shown within the **Impacted Clusters** section.  As you can see from the sample below, the cluster name is showing as **PHX-MKT250**.

   .. figure:: images/info_cluster_name.png

#. From within Prism Central, obtain the name of the Acropolis network your VM will connect to.

   - Select :fa:`bars` **> Virtual Infrastructure > Networks**.
   - As you can see from the sample below, the network name is showing as **Primary**.

     .. figure:: images/info_network_name.png


#. Edit the **blueprint.py** file so that it contains the following.  After you create and save the **blueprint.py** file, we will go through what each of the blueprint sections do.

   .. note::

      When editing the blueprint, replace **NETWORK** and **CLUSTER** with the network and cluster names obtained above.  This should be on or around line 47.

   .. note::

      Because the Nutanix Calm DSL is written using Python 3, it is important that the rules of standard Python development are followed.  In particular, pay attention to the indentation below.  Incorrectly indenting a Python file can result in unexpected behaviour.

   .. code-block:: bash

        """
        Sample blueprint for Nutanix Calm DSL Lab, August 2020
        """

        import json  # no_qa
        import os  # no_qa

        from calm.dsl.builtins import *  # no_qa


        # Secret Variables
        BP_CRED_SuperUser_KEY = read_local_file("superuser_private_key")

        # Credentials
        BP_CRED_SuperUser = basic_cred(
            "centos", BP_CRED_SuperUser_KEY, name="SuperUser", type="KEY", default=True,
        )


        CentosImage = vm_disk_package(
            name="CentosImage",
            description="https://cloud.centos.org/centos/7/images/",
            config={
                "image": {
                    "name": "CentOS-7.8-2003",
                    "type": "DISK_IMAGE",
                    "source": "https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-2003.qcow2",
                    "architecture": "X86_64",
                },
                "product": {"name": "CentOS", "version": "7.8"},
                "checksum": {},
            },
        )


        class BasicLinux(Service):

            pass


        class vmcalm_array_indexcalm_timeResources(AhvVmResources):

            memory = 1
            vCPUs = 1
            cores_per_vCPU = 1
            disks = [AhvVmDisk.Disk.Scsi.cloneFromVMDiskPackage(CentosImage, bootable=True)]
            nics = [AhvVmNic.NormalNic.ingress("NETWORK", cluster="CLUSTER")]

            guest_customization = AhvVmGC.CloudInit(
                filename=os.path.join(
                    "specs", "basiclinuxvm_cloudinit_data.yaml"
                )
            )


        class vmcalm_array_indexcalm_time(AhvVm):

            name = "vm-@@{calm_array_index}@@-@@{calm_time}@@"
            resources = vmcalm_array_indexcalm_timeResources


        class BasicLinuxVm(Substrate):

            os_type = "Linux"
            provider_type = "AHV_VM"
            provider_spec = vmcalm_array_indexcalm_time
            provider_spec_editables = read_spec(
                os.path.join("specs", "basiclinuxvm_editables.yaml")
            )
            readiness_probe = readiness_probe(
                connection_type="SSH",
                disabled=False,
                retries="5",
                connection_port=22,
                address="@@{platform.status.resources.nic_list[0].ip_endpoint_list[0].ip}@@",
                delay_secs="60",
                credential=ref(BP_CRED_SuperUser),
            )


        class BasicLinuxPackage(Package):

            services = [ref(BasicLinux)]

            @action
            def __install__():

                CalmTask.Exec.ssh(
                    name="ConfigureBaseVM",
                    filename=os.path.join(
                        "scripts",
                        "package_install_configure_base_vm.sh",
                    ),
                    cred=ref(BP_CRED_SuperUser),
                    target=ref(BasicLinux),
                )
                CalmTask.Exec.ssh(
                    name="ConfigureFirewall",
                    filename=os.path.join(
                        "scripts",
                        "package_install_configure_firewall.sh",
                    ),
                    cred=ref(BP_CRED_SuperUser),
                    target=ref(BasicLinux),
                )


        class basiclinuxvm_deployment(Deployment):

            min_replicas = "1"
            max_replicas = "1"
            default_replicas = "1"

            packages = [ref(BasicLinuxPackage)]
            substrate = ref(BasicLinuxVm)


        class Default(Profile):

            deployments = [basiclinuxvm_deployment]

            InstancePublicKey = CalmVariable.Simple(
                read_local_file("superuser_public_key"),
                label="",
                is_mandatory=False,
                is_hidden=False,
                runtime=True,
                description="",
            )


        class BasicLinuxVM(Blueprint):
            """Simple BP for working with the Nutanix Calm DSL."""

            services = [BasicLinux]
            packages = [BasicLinuxPackage, CentosImage]
            substrates = [BasicLinuxVm]
            profiles = [Default]
            credentials = [BP_CRED_SuperUser]

   **blueprint.py Structure**

   With `blueprint.py` created, let's briefly look at what each part does.  It is recommended to have `blueprint.py` open while reading this section so that line number comparisons are possible.

   - Line 12: The `superuser_private_key` file is read and stored in a variable named `BP_CRED_SuperUser_KEY`
   - Lines 20-33: A new `vm_disk_package` instance is created.  This defines the disk image that will be used as the base for our application's single VM
   - Lines 41-47: The VM's spec is defined, including CPU, RAM and the VM's single network interface card (NIC)
   - Lines 49-53: The Cloud-Init data stored in `specs/basiclinuxvm_cloudinit_data.yaml` are read and stored as a new `AhvVmGC.CloudInit` object
   - Lines 56-59: An instance of `AhvVm` is created, along with properties that define the VM's name and resources that were created in the previous section
   - Lines 62-78: The application's single VM substrate is created, specifying the VM's operating system, provider type and defining how Calm will test to see if the new VM is accessible
   - Lines 81-105: The application's single package is defined, including specifications for the two package install tasks that will run during deployment (ConfigureBaseVM and ConfigureFirewall)
   - Lines 108-115: The application's single deployment is defined, specifying service-specific settings such as the minimum and maximum number of instances per service (VM) and the packages that apply to that service
   - Lines 118-129: The application's single profile is defined, tieing together our previously defined deployment and also adding a runtime variable named `InstancePublicKey`
   - Lines 132-139: All previous defined blueprint components are "wired" together to create a Calm DSL blueprint, ready to be created on Prism Central

Create Blueprint on Prism Central
.................................

With the files created and the blueprint ready to be created on Prism Central, a single command can be used to do exactly that.

#. Within your Calm DSL container (which should still be running), continue as follows.  Replace **<initials>** with your initials.

   .. code-block:: bash

      cd ~/nutanix/BasicLinuxVM
      calm create bp --name BasicLinuxVM_<initials> --file blueprint.py

   .. figure:: images/basiclinuxvm_create.png

   As you can see from the screenshot above, a new blueprint named **BasicLinuxVM_<initials>>** has been created on the connected Prism Central instance.

#. We can now continue to the final step - launching the new blueprint!  Run the following command to do that.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm launch bp BasicLinuxVM-<initials> --app_name BasicLinux-<initials> --ignore_runtime_variables

   .. figure:: images/basiclinuxvm_launch.png

   As you can see from the screenshot above, our `BasicLinuxVM_<initials>` blueprint has been launched and an application created named `BasicLinuxVM_<initials>`. 


Summary
.......

This section of the lab covered the creation of a detailed Calm DSL blueprint from scratch:

- Learning the structure of a Calm DSL blueprint
- Looking at some of the instance types that can be leveraged
- Wiring the structure and those instances together to create a simple Calm application

In the next section we'll wrap things up and take a look back over what we've learnt.
