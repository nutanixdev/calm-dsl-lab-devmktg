Configuring the Nutanix Calm DSL
################################

Connecting to Prism Central
...........................

Before the Nutanix Calm DSL can be used, it must be configured with a connection to Prism Central.  The process for this involves a single-command initialisation process, during which we specify details about the Prism Central instance we'd like to use.

During that process, one of the required pieces of information is the name of the Prism Central Project to use with the Calm DSL.  Since a configured Calm environment will, in many cases, already have a project named 'default', please select 'default' when asked for the project name.

To configure the Calm DSL, proceed as follows.

#. Ensure the Nutanix Calm DSL container is running, as completed in previous steps.  Skip this step if the container is already running.

   .. code-block:: bash

      cd ~/nutanix/calm-dsl
      . venv/bin/activate
      make run

   .. figure:: images/confirm_container_running.png

#. Within the Calm DSL container, run the following command.  At each step, enter the details for your environment, paying particular attention to the project name.  If unsure, it suggested to use the 'default' project.

   .. note::

      The 'default' project can be used in environments where the 'default' project hasn't been deleted or renamed.

   .. code-block:: bash

      which calm
      calm init dsl

   .. figure:: images/calm_init_dsl.png

   As can be seen above, we have confirmed the `calm` command is available within our container and have then completed the Calm DSL configuration steps.  The Calm DSL is now ready to be used!

   .. note::

      IMPORTANT NOTE: Docker containers are inherently ephemeral by default.  This means files created within the container, **including Calm DSL configuration files**, are lost when the container is stopped.  For this reason, stopping the container will require you to run `calm init dsl` again when you restart the container.

Creating a lab project
......................

For today's lab, we will use a project specifically intended for our use and our use only.  In August 2020, the Calm DSL released a feature for project management, allowing us to create and manage projects directly using the Calm DSL itself.  Let's create a project now by using that feature.

#. From within Prism Central, obtain the name of your Prism Element cluster.  On the default Dashboard with a single Prism Element registered, this is shown within the **Impacted Clusters** section.  As you can see from the sample below, the cluster name is showing as **PHX-MKT250**.

   .. figure:: images/info_cluster_name.png

#. From within Prism Central, obtain the name of the Acropolis network your VM will connect to.

   - Select :fa:`bars` **> Virtual Infrastructure > Networks**.
   - As you can see from the sample below, the network name is showing as **Primary**.

     .. figure:: images/info_network_name.png

#. Within your container, create a file named **lab_project_<initials>.py**.  Replace **<initials>** with your initials.

#. Add the following content to **lab_project_<initials>.py**:

   .. code-block:: python

      from calm.dsl.builtins import Project
      from calm.dsl.builtins import Provider, Ref

      ACCOUNT = "NTNX_LOCAL_AZ"
      SUBNET = "<acropolis_network_name>"
      CLUSTER = "<cluster_name>"
      VCPUS = 4
      STORAGE = 10  # GiB
      MEMORY = 4  # GiB

      class TestDemoProject(Project):
          """Project created by the Calm DSL"""
      
          providers = [
              Provider.Ntnx(
                  account=Ref.Account(ACCOUNT),
                  subnets=[Ref.Subnet(name=SUBNET, cluster=CLUSTER)],
              ),
          ]

          quotas = {"vcpus": VCPUS, "storage": STORAGE, "memory": MEMORY}

   This file specifies a number of settings that will be specific to our project e.g. the cluster name, network name and some arbitrary quotas to apply to the new project.

#. Create a new project using the Calm DSL by running the following command.  Replace all instances of **<initials>** with your initials.

   .. code-block:: bash

      calm create project --file lab_project_<initials>.py --name "lab_project_<initials>" --description "Project for use with the Calm DSL Lab"

   As you can see from the screenshot below, the Calm DSL will create our new project as expected.

   .. figure:: images/create_lab_project.png

#. As a final step, we need to now tell the Calm DSL to use that project.  Run the following commands to complete this step.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm update cache
      calm set config --project lab_project_<initials>

   There's no output from this command, as shown below.

   .. figure:: images/set_lab_project.png

Summary
.......

In this quick section we completed the following steps:

- Ensured our Calm DSL container is running
- Verified the `calm` command is available; this step is mostly ornamental, since `calm` will always be available within the Calm DSL container
- Configured our Calm DSL container to connect to our Prism Central instance
- Created a new project specifically for our lab
- Reconfigured the Calm DSL to use our new project

In the next section we'll start using the Calm DSL to talk to Calm and start with working with blueprints.
