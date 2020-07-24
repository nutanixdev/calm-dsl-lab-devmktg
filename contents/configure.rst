Configuring the Nutanix Calm DSL
################################

Connecting to Prism Central
...........................

Before the Nutanix Calm DSL can be used, it must be configured with a connection to Prism Central.  The process for this involved a single-command initialisation process, during which we specify details about the Prism Central instance we'd like to use.

During that process, one of the required pieces of information is the name of the Prism Central Project to use with the Calm DSL.  Creation of a specific project is beyond the scope of this lab, since a configured Calm environment will, in nearly all cases, already have at least one project available.  If you are not sure which project to use, it is suggested you specify 'default' when asked.

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

Summary
.......

In this quick section we completed the following steps:

- Ensured our Calm DSL container is running
- Verified the `calm` command is available; this step is mostly ornamental, since `calm` will always be available within the Calm DSL container
- Configured our Calm DSL container to connect to our Prism Central instance

In the next section we'll start using the Calm DSL to talk to Calm and start with working with blueprints.
