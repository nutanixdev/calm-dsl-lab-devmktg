Launching Blueprints
####################

This short section has been split out from the other blueprint content as it's a very important part on its own.  We'll work on one specific type of blueprint launch - our own `HelloBlueprint_<initials>` blueprint that was created earlier in this lab.

In previous steps we've already covered the installation of the Calm DSL, run (or build) of Calm DSL Docker container, launching the container and configuration of the Calm DSL within the container itself.  For this section, please ensure your Docker container is running and that you have completed the configuration steps by running `calm init dsl`.

Launching HelloBlueprint
........................

Before launching the blueprint for the first time, we'll quickly make sure the blueprint has been uploaded and is available on Prism Central.

#. From within your Calm DSL container, carry out a quick blueprint search by name.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm get bps --name HelloBlueprint_<initials>

   .. figure:: images/calm_get_bps_name.png

   If everything has been successful up to this point, we'll see **HelloBlueprint_<initials>** available in the filtered list.

Direct Launch
~~~~~~~~~~~~~

#. Before launching the blueprint for the first time, let's take a quick look at the command options made available by the Calm DSL.  The command used here is **almost** the same as that used to launch the blueprint in the next step, but with a request for help added.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm launch bp HelloBlueprint_<initials> --app_name HelloApp_<initials> --help

   .. figure:: images/launch_bp_help.png

   As you can see in the screenshot above, an extensive collection of help is provided.  Of particular note are the **-i** and **-l** parameters.  We'll use **--launch_params** shortly so that variables values can be passed at launch time.  **--ignore_runtime_variables** will instruct the Calm DSL to use variable defaults.

#. Create a file somewhere within the container named **runtime_variables.py**.

   - Create the file:

     .. code-block:: bash

        cd ~
        vi ./runtime_variables.py

   - Press **i** to put `vi` into insert mode
   - Paste the following contents into the file using Ctrl-Shift-V (Windows/Linux) or Cmd-Shift-V (Mac)

     .. code-block:: python

        variable_list = [
            {"value": {"value": "hello"}, "context": "HelloProfile", "name": "var1"},
            {"value": {"value": "goodbye"}, "context": "HelloProfile", "name": "var2"},
        ]

     .. note::

    - Save the **runtime_variables.py** and exit using the following key sequence:

      - ESC
      - :wq
      - ENTER

#. As mentioned in previous steps, **HelloBlueprint_<initials>** was created with some default credentials in the form of an SSH key pair.  We don't need to generate our own and can now launch the blueprint, along with specifying the runtime variables file created in the previous step.  Enter the following command to launch the **HelloApp_<initials>** app.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm launch bp HelloBlueprint_<initials> --app_name HelloApp_<initials> --launch_params runtime_variables.py

   The result of running this command is shown in easily parse-able JSON chunks, each representing a step in the launch process.  Because we chose to specify runtime variable values via **runtime_variables.py**, let's focus on that section first.

   .. figure:: images/launch_params_result.png

   Note the highlighted sections that match values from our **runtime_variables.py** file.

Marketplace Launch
~~~~~~~~~~~~~~~~~~

One of the most powerful features of Nutanix Calm is the ability to take a complete application design and publish it to the built-in Calm Marketplace.  By doing this, application publication and launch can be controlled by granting access to specific users and/or groups.

The Calm DSL has built-in features to work with the Nutanix Calm Marketplace, one of which is the ability to take our HelloBlueprint app and make it available for launch directly from the Marketplace.  Let's do that now.

#. First, use the following command to take a look at existing Marketplace items.  These items are already approved and available for launch from Nutanix calm.

   .. code-block:: bash

      calm get marketplace_items

   .. figure:: images/calm_get_marketplace_items.png

#. Similar to the way we searched for a blueprint by name, we can also search for a specific marketplace item by name.  Use the following command to search for an marketplace item with the name **Hadoop**.

   .. code-block:: bash

      calm get marketplace_items --name Hadoop

   .. figure:: images/calm_get_marketplace_items_name.png

#. As seen in previous steps, we now know the **--help** parameter can be used at any time.  This parameter will provide context-sensitive assistance on the available options available after completing the current partial command.  Publishing our HelloBlueprint blueprint is done with the **publish** command, meaning we can use the following command to get a list of parameters we may want to use.

   .. code-block:: bash

      calm publish --help

   We'll see it in the screenshot shortly, but the first thing to note is that we're told of the ability to use **bp** as a parameter for the **publish** command.  Following that process, we can then use the following command to get even more context-sensitive help:

   .. code-block:: bash

      calm publish bp --help

   And now, after running that command, we're presented with the list of parameters we need:

   .. figure:: images/calm_publish_help.png

#. Although only the **--version** parameter is listed as mandatory, many of the parameters will make publication of our blueprint much more logical.  For example, **--name** and **--description** aren't mandatory parameters, but it would make sense to use them here.  Run the following command to publish **HelloBlueprint_<initials>** to the Calm Marketplace, after which we can launch the app directly from the marketplace.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm publish bp HelloBlueprint_<initials> --version v1.0.0 --name HelloApp_<initials> --description "HelloApp from the Calm DSL" --with_secrets --publish_to_marketplace --project lab_project_<initials> --category DevOps

   .. figure:: images/calm_publish_bp.png

   What happened here?  Let's break down the process the Calm DSL went through.

   - A search was carried out to make sure the name **HelloApp_<initials>** isn't already in use
   - The HelloBlueprint_<initials> blueprint was successfully found
   - HelloBlueprint_<initials> was successfully published to the marketplace manager
   - The **lab_project_cr** project was found
   - The HelloApp_<initials> marketplace blueprint was approved as ready for use by marketplace users

   In many cases, it may be desirable for the marketplace blueprint to be published but not approved.  In this case, the command can be altered as follows.  Note the removal of the **--publish_to_marketplace** parameter.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm publish bp HelloBlueprint_<initials> --version v1.0.0 --name HelloApp_<initials> --description "HelloApp from the Calm DSL" --with_secrets --project lab_project_<initials> --category DevOps

#. **Please note this important step.**  As the time this lab was being developed, the Calm DSL didn't yet support programmatic control of the Project Environments.  Project Environments define default credentials, VM specs (etc) that will be used if they aren't already specified by a blueprint.

   For now, we will have to configure the environment manually - please continue below.

   - Login to Prism Central using your credentials
   - Click the `Projects`

     .. figure:: images/projects_button.png

   - Click the name of your project i.e. **lab_project_<initials>** and note the exclamation point indicating the environment needs attention

     .. figure:: images/project_environment.png

   - Click the "+" button next to **Credentials** and complete all fields, as shown; this credential won't actually be used so you may enter any values for these fields

     .. figure:: images/environment_create_cred.png

   - Configure the VM specs as shown; as with the credential these values won't actually be used, so you may set them to any values you choose

     .. figure:: images/environment_configure_specs.png

   - Scroll to the bottom of the environment section, click the "+" button next to "NETWORK ADAPTERS (NICS)" and configure all fields as shown; the network name can be set to any value, with the credential value being set to the credential you created earlier.

     .. figure:: images/environment_configure_nic.png

   - Click Save

#. With **HelloBlueprint_<initials>** now published as **HelloApp_<initials>**, let's use the Calm DSL to launch **HelloApp_<initials>**.  As with the context-sensitive help options used in previous steps, the Calm DSL help can be utilised along the way to make sure our commands are correct.  Replace **<initials>** with your initials.

   .. code-block:: bash

      calm launch marketplace_item HelloApp_<initials> --version 1.0.0 --app_name HelloApp_<initials>_1_0_0 --project lab_project_<initials> --ignore_runtime_variables

   As shown in the screenshot below, the Calm DSL has utilised the Calm APIs to launch **HelloApp_<initials>** from the Calm Marketplace.

   .. figure:: images/calm_launch_marketplace_item.png

#. While the **HelloApp_<initials>_1_0_0** application is being provisioned, use the following command to check on the status of the deployment:

   .. code-block:: bash

      calm get apps --name HelloApp_<initials>_1_0_0

   As you can see in the screenshot below, the **STATE** of the **HelloApp_<initials>_1_0_0** application is **provisioning**.

   .. figure:: images/calm_get_apps_name.png

Summary
.......

This section of the lab covered the high-level specifics of blueprint launches:

- Launching a blueprint directly from the Calm DSL
- Publishing HelloBlueprint to the Calm Marketplace
- Launching HelloBlueprint from the Calm Marketplace, via HelloApp_<initials>

In the next section we'll take a look at some of the ways blueprints can be shared between application design and automation teams: Blueprint Decompile.