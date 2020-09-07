.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_


Integrate Geospatial tools 
--------------------------

**Description:**

In this section, we will introduce |Geospatial| tools and their dependancies

  .. admonition:: Discussion - Conda vs. Docker Containers

     Why and when should we use Conda or Docker?


We suggest taking time to work through the |Creating and Running Docker Containers| tutorial
before this section, especially if you have not used containers before.

*Using a Data API Description*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You need some rationale for which tools you use and why. For
our tutorial example we will perform the following steps with the
following containers:

.. list-table::
    :header-rows: 1

    * - Step
      - Description
      - Tool
      - Command Line
    * - Create a new directory
      - Make a new directory in your home directory
      - Command Line (Bash)
      - ``mkdir ~/NEON_Downloads``
    * - Change the permissions on the directory so Docker can write to it
      - The Shiny App runs using its own user ID (``uid``) and group ID (``gid``) inside the container -- these may not be the same on your host and definitely not on the VM
      - Command Line (Bash)
      - ``sudo chown -R 999:10013 NEON_Downloads/``
    * - Get a Docker Shiny App that interfaces with NEON Data API
      - In this tutorial we won't be using data we produced, rather we'll be using data from NEON.
      - |neon tools|
      - ``docker run -it --rm -p 3838:3838 -e REDIRECT_URL=http://localhost:3838 -v ${HOME}/NEON_Downloads:/srv/shiny-server/NEON_Downloads cyversevice/shiny-neon-browser:3.6.3``
    * - Browse for NEON AOP Data
      - Select any AOP data set and download it
      - |neon-shiny-browser|
      - 
    * - Check that the data are there
      - Navigate back to the shiny home page
      - Command Line (Bash)
      - ``ls -lh ~/NEON_Downloads``

You've just learned how to download data from an API, using a Docker Container running an R Shiny App (whoa!)

You should now have data in a directory on your VM that you can do an analysis on.

*Using a IDE Description*
~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
    :header-rows: 1    
    
    * - Step
      - Description
      - Tool
      - Command Line
    * - Get a Jupyter Lab Docker container
      - Start a Jupyter Lab instance with geospatial packages already installed
      - 
      - ``docker run -it --rm -v ${HOME}/NEON_Downloads/:/home/jovyan/NEON_Downloads -p 8888:8888 -e REDIRECT_URL=http://localhost:8888 cyversevice/jupyterlab-gee:latest``
    * - Load NEON AOP Data
      - Navigate to the directory you downloaded the data to
      - 
      - 
    * - Get some Notebooks
      - use ``git`` in the Jupyter Lab to pull some pre-written notebooks from `Qiusheng Wu <https://github.com/giswqs>`_ GitHub
      - Command Line (Bash)
      - ``git clone https://github.com/giswqs/earthengine-py-notebooks``

With the Jupyter Lab you also have access to a terminal, a notebook interface, and the ability to interact with version control on GitHub or GitLab all through your Browser.

*Exploring Data using Containers*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once you have identified the container of interest, you need to explore how to
run the container and the tool itself. For example,let's build a two-step
workflow with some sample data. 

1. We can also get NEON lidar data from the CyVerse Data Store:

   .. code-block:: bash
    
     $ iinit
       One or more fields in your iRODS environment file (irods_environment.json) are
       missing; please enter them.
       Enter the host name (DNS) of the server to connect to: data.cyverse.org
       Enter the port number: 1247
       Enter your irods user name: your_username
       Enter your irods zone: iplant
       Those values will be added to your environment file (for use by
       other iCommands) if the login succeeds.
       
   .. code-block:: bash

     mkdir ${HOME}/NEON_Downloads

     cd ~/NEON_Downloads

     iget -KPbvrf /iplant/home/shared/cyverse_training/datasets/NEON_HARV_DP1.30003.001_2019/2019/FullSite/D01/2019_HARV_6/L1/DiscreteLidar/ClassifiedPointCloud/NEON_D01_HARV_DP1_726000_4707000_classified_point_cloud_colorized.laz

2. We can run ``pdal`` on one of the files using a container from Docker Hub

   .. code-block:: bash

     docker run pdal/pdal:latest pdal info NEON_D01_HARV_DP1_726000_4707000_classified_point_cloud_colorized.laz -p 0

When it finishes, we can see the output in the directory

    .. code-block:: bash
      
      PDAL: Unable to open stream for 'NEON_D01_HARV_DP1_726000_4707000_classified_point_cloud_colorized.laz' with error 'No such file or directory'

The problem is that the docker container can not "see" the input file; we need
to become more familiar with the Docker |run command|. 

We now see that we need to mount our local (Atmosphere) disk so that the file can be seen inside the container:

3. Rerun the docker command using the `-v` option.

   .. code-block:: bash

     # add the atmosphere present working directory or $PWD as a volume -v
     # and give it a new name as a directory data/ which will be mounted in the container
     docker run -v ${PWD}:/data pdal/pdal:latest pdal info data/NEON_D01_HARV_DP1_726000_4707000_classified_point_cloud_colorized.laz -p 0

Now, the app ``pdal`` can see the file and outputs the metadata for the first point ``0`` in the file.

   .. code-block:: bash
   
      {
        "file_size": 42376072,
        "filename": "/data/NEON_D01_HARV_DP1_726000_4707000_classified_point_cloud_colorized.laz",
        "now": "2020-09-06T22:52:18+0000",
        "pdal_version": "2.1.0 (git-version: 13d68e)",
        "points":
        {
          "point":
          {
            "Blue": 27648,
            "Classification": 5,
            "EdgeOfFlightLine": 0,
            "GpsTime": 232777.8343,
            "Green": 25088,
            "Intensity": 6,
            "NumberOfReturns": 1,
            "PointId": 0,
            "PointSourceId": 2111,
            "Red": 17152,
            "ReturnNumber": 1,
            "ScanAngleRank": -17,
            "ScanDirectionFlag": 0,
            "UserData": 57,
            "X": 726326.78,
            "Y": 4707499.95,
            "Z": 246.96
          }
        },
        "reader": "readers.las"
      }


4. Try running ``pdal`` on the sample ``.laz`` file above using another one of its tools. `https://pdal.io/quickstart.html <https://pdal.io/quickstart.html>`_



  .. admonition:: Discussion - Bash script

     Is this Bash script a good solution?
     What problems could we run into when making our larger workflow?
     What could improve this script?


----

**Fix or improve this documentation**

Search for an answer:
|CyVerse Learning Center|

----

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_

.. Comment: Place Images Below This Line
   use :width: to give a desired width for your image
   use :height: to give a desired height for your image
   replace the image name/location and URL if hyperlinked


 .. |Clickable hyperlinked image| image:: ./img/IMAGENAME.png
    :width: 500
    :height: 100
 .. _CyVerse logo: http://learning.cyverse.org/

 .. |Static image| image:: ./img/IMAGENAME.png
    :width: 25
    :height: 25

.. Comment: Place URLS Below This Line

   # Use this example to ensure that links open in new tabs, avoiding
   # forcing users to leave the document, and making it easy to update links
   # In a single place in this document

   .. |Substitution| raw:: html # Place this anywhere in the text you want a hyperlink

      <a href="REPLACE_THIS_WITH_URL" target="blank">Replace_with_text</a>


.. |Github Repo Link|  raw:: html

   <a href="https://github.com/JasonJWilliamsNY/cyverse-reproducbility-tutorial" target="blank">Github Repo Link</a>


.. |neon-shiny-browser|  raw:: html

   <a href="https://github.com/neon-shiny-browser/" target="blank">neon-shiny-browser</a>

.. |Creating and Running Docker Containers|  raw:: html

   <a href="https://cyverse-creating-docker-containers-quickstart.readthedocs-hosted.com/en/latest/" target="blank">Creating and Running Docker Containers</a>

.. |neon tools|  raw:: html

   <a href="https://data.neonscience.org/" target="blank">neon tools</a>

.. |run command|  raw:: html

   <a href="https://docs.docker.com/engine/reference/commandline/run/" target="run command">LINK</a>

.. |bash script|  raw:: html

   <a href="https://www.linux.com/tutorials/writing-simple-bash-script/" target="blank">bash script</a>

.. |Data Carpentry Geospatial Workshop|  raw:: html

   <a href="https://datacarpentry.org/geospatial-workshop/">Data Carpentry Geospatial Workshop</a>
