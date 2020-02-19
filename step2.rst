.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_


Computer and project setup with Github, Conda, and the Data Store
-------------------------------------------------------------------

**Description:**

In this section, we will be setting up our computer with some tools we will
need. We will also start our initial folder.

  .. note::

    **Using Atmosphere**

    Most if not all of this tutorial can be done on a local computer. However,
    we will assume you are using the |Atmosphere Image| we suggested at the
    beginning of the tutorial. If you are using your own computer, anticipate
    that some steps may be slightly different on your own system.

----



*Connect to the Atmosphere VM*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Following the instructions in the |Atmosphere Guide| launch
   the recommended |Atmosphere Image| and then use `ssh` to
   connect to the instance.

*Make a local clone of your Github repository*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Once you have connected to Atmosphere, you can
now clone the GitHub repository to that computer.

1. Go to your GitHub repository and click the :guilabel:`&Clone or download`
   button. Copy the URL that is provided.

2. For much of this tutorial we are going to be working as the root user
   so you can switch to root in Atmosphere using this command:

   .. code-block:: bash

     $ sudo su

   .. tip::

     Your CyVerse password is your root password

3. Next, we will need to clone our GitHub repository. In Atmosphere
   we have a large amount of disk space mounted at `/scratch` so we
   will change to that directory and clone there:

   .. code-block:: bash

     cd /scratch

     # make sure to use your actual URL
     git clone "YOUR GITHUB REPO URL"

*Install Conda*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

|Conda| is a popular tool for installing software. Typically
software you want to use requires other software (dependancies)
to be installed. Conda can manage all of this for you. Each
available Conda package is part of a "recipe" that includes
everything you need to run your software. There are different
versions of Conda, including some specific for bioinformatics like
|Bioconda|. We will install Conda and then use it to install
some of the tools we need. We will install a lightweight version
of Conda called |MiniConda|.


**Install MiniConda**

1. We can install MiniConda with the following code:

  .. code-block:: bash

    # download the Miniconda installer
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

    # instal miniconda silently (-b) in path (-p) /opt/conda
    bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda

    #make sure all conda packages will be in path by symbolic links to /bin
    #this step is a bit of a hack and you may get some warnings about
    #symbolic links that cannot be created - it's ok.
    ln -s /opt/conda/pkgs/*/bin/* /bin
    ln -s /opt/conda/pkgs/*/lib/* /usr/lib





*Install and test JupyterLab*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Install Jupyter, bash kernel, and dependencies using conda:

  .. code-block:: bash

    # Install Jupyter lab version 1.2.3
    /opt/conda/bin/conda install -c conda-forge -y jupyterlab=1.2.3
    /opt/conda/bin/conda install -c conda-forge -y nodejs=10.13.0

    /opt/conda/bin/pip install bash_kernel
    /opt/conda/bin/pip install ipykernel
    /opt/conda/bin/python3 -m bash_kernel.install
    # if the above fails try /opt/conda/bin/conda install -y ipykernel

  .. tip::

     It is a good idea in your code to specifically indicate the
     versions of software packages you are installing (e.g. jupyterlab=1.2.3).
     You can search for available package versions on conda using the
     command:

     .. code-block:: bash

        conda search <PACKAGE NAME>

     You can then install the version of choice.


2. We can start jupyterlab with the following command:

  .. code-block:: bash

    /opt/conda/bin/jupyter lab --no-browser --allow-root --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.password='' --notebook-dir='/scratch/reproducibility-tutorial/'

3. You can access your Jupyter lab session by opening a web browser and
   entering the IP address of your Atmosphere instance followed by `:8888`
   (e.g. 123.45.67:8888) (press CNTL+C to shutdown/exit)


*Install and test snakemake*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Next we will use conda to |install snakemake|

1. Check for snakemake on the bioconda channel

  .. code-block:: bash

    # See what snakemake version are available
    /opt/conda/bin/conda search -c bioconda snakemake

    # Let's choose the 5.8.1.
    /opt/conda/bin/conda install -c bioconda -c conda-forge -y snakemake=5.8.1

    # hack conda again

    ln -s /opt/conda/bin/* /bin
    ln -s /opt/conda/lib/* /usr/lib

    # verify the installation
    snakemake --version

*Install and test Docker*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We will |install Docker| as recommended for Linux:

  .. code-block:: bash

    # update ubuntu apt-get package manager
    sudo apt-get update

    # install some needed packages
    # It's ok to say yes to any warnings
    sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

    # add the Docker key
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

    #add the repository
    sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"

    # update apt-get with new repository information
    sudo apt-get update

    # install docker
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io

    #test docker
    docker run hello-world


*Setup your local filesystem*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Now that we have setup the basic software we will need, we should
setup some of the file system we want for our project.
|A Quick Guide to Organizing Computational Biology Projects| is
still a good reference and we will borrow some of those recommendations.

1. Use the history command to document the installation steps (and
   software versions) in our installation process:

  .. code-block:: bash

    # change into our project directory
    cd /scratch/reproducibility-tutorial/

    # append your bash history to the README.MD file
    history >>README.md

    # Use nano to edit the history down to the relevant commands
    # use markdown to make this into a readable report
    nano README.md

  After editing, a well-documented README may look something like...

  .. code-block:: md

        # reproducibility-tutorial

    ## Computer Setup

        #download the Miniconda installer
        wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

        # instal miniconda silently (-b) in path (-p) /opt/conda
        bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda

        #make sure all conda packages will be in path by symbolic links to /bin
        ln -s /opt/conda/pkgs/*/bin/* /bin
        ln -s /opt/conda/bin/* /bin
        ln -s /opt/conda/pkgs/*/lib/* /usr/lib

        # Install Jupyter lab version 1.2.3
        /opt/conda/bin/conda install -c conda-forge -y jupyterlab=1.2.3
        /opt/conda/bin/conda install -c conda-forge -y nodejs=10.13.0

        python3 -m pip install bash_kernel
        pip install ipykernel
        python3 -m bash_kernel.install

        #Test Jupyterlab
        jupyter lab --no-browser --allow-root --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.password='' --notebook-dir='/scratch/reproducibility-tutorial/'

        #Install Snakemake
        /opt/conda/bin/conda install -c bioconda -c conda-forge -y snakemake=5.8.1

        #install Docker
        # update ubuntu apt-get package manager
        sudo apt-get update

        # install some needed packages
        sudo apt-get install -y \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common

        # add the Docker key
        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

        #add the repository
        sudo add-apt-repository \
         "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
         $(lsb_release -cs) \
         stable"

        # update apt-get with new repository information
        sudo apt-get update

        # install docker
        sudo apt-get install -y docker-ce docker-ce-cli containerd.io

        #test docker
        docker run hello-world

Ordinarily, we might we might create a few folders for our raw data, and
we will get to those steps shortly. For now, let's get the metadata from
our SRA experiment. Unfortunately, SRA does not have a way to automatically
do this, so we will we go to the **SRA Run Selector** for the chosen sample data.

1. Go to |SRA Study SRP170758| and click on `Metadata` to download
   `SraRunTable.txt` and save the table (text file) to your computer.

We will use |scp| to copy the SraRunTable.txt from our local system to our
Atmosphere instance (If you are using Widows and don't have
|Windows Linux Subsystem| installed, you could also try |pscp.exe| from the
PuTTY project.

2. First **On your Atmosphere Computer** change the permissions on
   `/scratch/reproducibility-tutorial/` so that the account
   associated with your CyVerse username can write to it:

    .. code-block:: bash

        chown -R YOURCYVERSEUSERNAME /scratch/reproducibility-tutorial/

3. On your local computer, do an `scp` transfer to upload your data to
   to the Atmosphere computer. Remember to adjust the code below to
   reflect your 1) the location of the download **SraRunTable.txt**,
   2) CyVerse username and 3) Atmosphere password.


    .. code-block:: bash

        scp ~/Downloads/SraRunTable.txt YOURCYVERSEUSERNAME@123.456.78.9:/scratch/reproducibility-tutorial/

  .. tip::

    We won't cover it here, but once you have this table, you can use various
    bash shell commands to parse the data and even create a metadata file that
    you can use to upload to CyVerse

      .. code-block:: bash

        # get the first column from the SraRunTable.txt delimited by commas
        cut -f1 SraRunTable.txt -d ,

4. Create an `experiment` folder and then an `sra_files\metadata` folder
   to keep with the SRA files. This can eventually be used as metadata
   you associate with files when you upload to CyVerse (See: |Data Store Guide| -
   associating data with metadata)

      .. code-block:: bash

       # -p flag makes the last directory and all prior directories needed to
       # complete the path you have described

       mkdir -p /scratch/reproducibility-tutorial/experiment/sra_files/metadata
       mv SraRunTable.txt experiment/sra_files/metadata/


*Commit to GitHub and copy your work to the Data Store*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We can now save our work to GitHub (although we could have committed
after we updated our README - which we may also want to update
with a note about the metadata).

1. Commit and push changes to GitHub

   .. code-block:: bash

     # check what files git is tracking
     git status

     #If you created Jupyter notebooks .ipnyb files or a
     # `.ipynb_checkpoints/` directory delete these

     # add the experiment directory to version control
     git add experiment/

     # commit changes with a helpful message
     git commit -am "update readme and record project metadata"

     #push changes to github
     git push

2. Make a directory on CyVerse Data Store and push this to CyVerse
   (See |Data Store Guide| - iCommands if you are unfamiliar with
   iCommands).

    .. code-block:: bash

      # Setup iCommands (See Data Store Guide for more info)
      iinit

      #make a directory
      imkdir reproducibility_tutorial

      # copy files
      iput -rPV /scratch/reproducibility-tutorial reproducibility_tutorial

   .. note::

     Right now we don't have any substantial data in our repository.
     Keep in mind, Git/GitHub won't let you version control large files
     so you will need to avoid tracking these files. See |Ignoring files|
     for tips - files with extensions like `.fastq`,`.fastq.gz`,`.sra`,
     `.bam`, etc. are good ideas to exclude. Of course the Data Store
     was meant for big data like this.

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

.. |Atmosphere Image|  raw:: html

   <a href="https://atmo.cyverse.org/application/images/1552" target="blank">Atmosphere Image</a>

.. |conda|  raw:: html

   <a href="https://docs.conda.io/en/latest/" target="blank">conda</a>

.. |Bioconda|  raw:: html

   <a href="https://bioconda.github.io/" target="blank">Bioconda</a>

.. |MiniConda|  raw:: html

   <a href="https://docs.conda.io/en/latest/miniconda.html" target="blank">MiniConda</a>

.. |install Snakemake|  raw:: html

   <a href="https://snakemake.readthedocs.io/en/stable/getting_started/installation.html" target="blank">install Snakemake</a>

.. |install Docker|  raw:: html

   <a href="https://docs.docker.com/install/linux/docker-ce/ubuntu/" target="blank">install Docker</a>

.. |A Quick Guide to Organizing Computational Biology Projects|  raw:: html

   <a href="https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1000424" target="blank">A Quick Guide to Organizing Computational Biology Projects</a>

.. |scp|  raw:: html

   <a href="https://haydenjames.io/linux-securely-copy-files-using-scp/" target="blank">scp</a>

.. |pscp.exe|  raw:: html

   <a href="https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html" target="blank">pscp.exe</a>

.. |SRA Study SRP170758|  raw:: html

   <a href="https://www.ncbi.nlm.nih.gov/Traces/study/?acc=SRP170758&o=acc_s%3Aa" target="blank">SRA Study SRP170758</a>

.. |ignoring files|  raw:: html

   <a href="https://help.github.com/en/github/using-git/ignoring-files" target="blank">ignoring files</a>
