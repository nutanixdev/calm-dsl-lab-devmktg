Calm DSL Blueprints
###################

Introduction
............

Before continuing, let's first take a moment to consider how blueprints look today.  At the simplest level, a Calm Blueprint is a collection of components:

- Profiles that indicate where an application can be deployed
- Services (VMs) that specify the resources responsible for running the app e.g. a web server
- Packages that specify the tasks that run as part of the app's deployment process
- Actions that allow the user to run tasks before, during or after an app is deployed

Conceptually this isn't anything new, since Nutanix Calm has been doing this for some time.  These blueprints are typically created using the intuitive and user-friendly Nutanix Calm user interface.

When it comes to automation, however, creating an application using a human-driven mouse + keyboard methodology isn't all that logical.  It's certainly possible to drive a UI via browser interface automation or scripting, but that would be really inefficient.

Instead, automation engineers and DevOps architects would typically be more drawn towards an interface that allows these to be done in a more programmatic way.  For some time Nutanix Calm has exposed a wide array of APIs that can be accessed by talking to Prism Central via a REST interface.  These work really well and are covered in depth throughout the free `Nutanix Calm REST APIs <https://www.nutanix.dev/labs/nutanix-calm-rest-apis/>`_ lab.

Nutanix Calm DSL Commands
~~~~~~~~~~~~~~~~~~~~~~~~~

To use the DSL, all commands begin with `calm`.  You've had exposure to this already - in an earlier section of this lab we used the DSL to configure itself by running `calm init dsl`.

Every command we'll run throughout today's lab will follow this convention.

Creating a Blueprint
....................

The first thing we'll do is use the Nutanix Calm DSL to create a blueprint for us.  By doing this, we are avoiding the need to manually create or import a blueprint for use with this lab.

#. If your Docker container is not already running within a virtual environment, please complete the first steps below.  If your Docker container is still running and configured from previous steps, please skip to step 2.

   **When prompted for the project name, ensure you use the name of your own project as created in the previous step.**

   .. code-block:: bash

      cd ~/nutanix/calm-dsl
      . venv/bin/activate
      make run
      calm init dsl

   .. figure:: images/calm_init_dsl.png

#. During the Calm DSL initialisation process, the help text made specific mention of what we're about to do next - create our sample blueprint.  Here's how the help text looked:

   .. note::

      HINT: To get started, follow the 3 steps below:
      1. Update the Calm DSL cache then initialize an example blueprint using the DSL: calm init bp
      2. Create and validate the blueprint: calm create bp --file HelloBlueprint/blueprint.py
      3. Start an application using the blueprint: calm launch bp Hello --app_name HelloApp01 -i

   Let's do that now:

   .. code-block:: bash

      calm update cache
      calm init bp

   .. figure:: images/calm_init_bp.png

   What actually happened, though?  We can see from the response above that the sample blueprint was generated and saved into `/root/HelloBlueprint/blueprint.py`.  Let's take a look at what's in that directory.
   
   .. code-block:: bash

      find /root/HelloBlueprint/

   .. figure:: images/blueprint_files.png

   Within our `HelloBlueprint` directory we can see a number of files have been created for us.

   - A `scripts` directory.  Scripts that are used during VM configuration are stored here.
   - A `.local` directory.  This hidden directory can be used to store credentials that apply to this blueprint but that should not be stored in source control.  If you were take a look at the files named `.local/keys/centos_pub` and `.local/keys/centos` you would see the Calm DSL has created a default SSH key pair for you.  We'll leave these in place as it greatly simplifies today's lab.
   - A file named `blueprint.py`.  This is the blueprint itself and pulls together all the specs, scripts and keys that we'll look at shortly.

   Before continuing with further commands using the Calm DSL, it is important to understand the structure of `blueprint.py` as it was generated in the previous step.

   Open `/root/HelloBlueprint/blueprint.py` and look at each of the sections.

   .. code-block:: bash

      more /root/HelloBlueprint/blueprint.py

   .. note::

      The **more** command will show a page of content at a time.  Press **SPACE** to go to the next page or **q** to quit at any time.

   As an introduction, one of the great things about the generated blueprint.py file is that it documents a lot of itself within itself.  The key points are as follows.

   - The required Python modules and libraries are imported right the start.
   - An SSH credential with username “centos” and the public key contained in “keys/centos_pub” is created. This is a good example of one of the files we can modify to suit our needs i.e. the credential’s public key file.
   - A Calm “configuration” is defined that specifies where Calm can look for this blueprint’s disk images. In this context, a disk image is the base disk used when creating our application VMs.
   - Calm “Services” i.e. the VMs that will be deployed as part of this example application.
   - As part of the `HelloService` service, we can see that service "actions" have also been defined.  These service actions specify the tasks can be run as part of the app's deployment, as well as an example of a task that can be run after the app has finished deploying.
   - In a similar way, we are then defining the packages that run during application deployment. Packages, in this example, are the installation and uninstallation scripts that run when the application is either created or destroyed.
   - The blueprint’s single VM specifications are defined, i.e. the number of vCPUs, RAM and disks, as well as a Cloud-Init script that creates our previously-defined CentOS credential.
   - The VM itself is defined based on the previous VM specifications. Doing it this way means we can specify a set of specifications that can be applied to multiple VMs e.g. those that all have the same spec. This is very common in applications that use clustered services, for example.
   - Our main deployment is then defined. Note that the deployment inherits the previously-created package and substrate definitions.
   - The application profile is created, including items such as profile variables that can be specified by the user at runtime.
   - As a final step, the main blueprint item is defined as an instance of the Blueprint type. This blueprint instance has properties that are created using the credentials (etc) that were in previous steps. The method shown here nicely packages everything into a clean and easy-to-read object, making the blueprint specification very easy to understand.

   Let's now take the sample blueprint, upload it to Prism Central and compare what we see in the Calm UI with the list of key points above.

#. Within your Calm DSL container, use the following command to upload the blueprint to Prism Central.  Replace **<initials>** with your initials.

   .. code-block:: bash
   
      calm create bp --file /root/HelloBlueprint/blueprint.py --name HelloBlueprint_<initials>

   .. figure:: images/upload_blueprint.png

   As you can see, the Calm DSL has gone through a process of verifying the `blueprint.py` and associated files are valid (an error would be returned if they weren't), and has created a blueprint named **HelloBlueprint_<initials>** on our Prism Central instance.

#. Login to Prism Central using your credentials.

#. Select :fa:`bars` **> Services > Calm**.

#. Select **Blueprints** in the left hand toolbar to view and manage Calm blueprints.

   .. note::

      Mousing over an icon will display its title.

#. Select **HelloBlueprint_<initials>** - this is the blueprint uploaded by the Calm DSL in previous steps.

   Note that all settings specified in our `blueprint.py` and associated scripts files are present in the UI.

   .. figure:: images/helloblueprint_ui.png

   - Click **HelloSubstrate** on the Calm UI blueprint designer
   - On the right side of the Calm UI, select **Package**
   - Select **Configure install**
   - Select **Task1**
   - Note the script that is made available for editing in the **Script** field.

     .. figure:: images/pkg_install_task_2.png

   - Also note the package install script was loaded from the **/root/HelloBlueprint/scripts/pkg_install_task.sh** file created with the sample blueprint:

     .. figure:: images/pkg_install_task.png

Working with Blueprints
~~~~~~~~~~~~~~~~~~~~~~~

Now that we have used the Calm DSL to create a sample blueprint, uploaded that blueprint to Prism Central and are aware of uploaded results, let's take a look at some of the other blueprint management functions available in the Calm DSL.

This section will be relatively short so that we can move on to the next section.

- List all existing blueprints on our Prism Central instance (up to a maximum of 20)

  .. code-block:: bash

     calm get bps

  .. figure:: images/calm_get_bps.png

  Note our **HelloBlueprint_<initials>** blueprint and **lab_project_<initials>** project.

- Search for a specific blueprint by name.  Replace **<initials>** with your initials.

  .. code-block:: bash

     calm get bps --name HelloBlueprint_<initials>

  .. figure:: images/calm_get_bps_name.png

Summary
.......

In this section we covered a number of different blueprint-specific points.

- Using the Calm DSL to create a sample blueprint
- Looked at the file structure of an example DSL blueprint
- Went over the highlights within `blueprint.py`
- Uploaded the sample blueprint to Prism Central

In the next section we will launch our blueprint!
