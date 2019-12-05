.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_

**Reproducibility Tour**
============================

Goal
----

This tutorial is intended to give you a "tour" of how you can build your own
reproducible analyses utilizing CyVerse resources. In reality, building a
reproducible workflow could take days, weeks, or longer. This short look will
point out some tools and resources you can use along your path.

----

.. toctree::
	:maxdepth: 2

	Tutorial home <self>
	Intro and Github setup <step1.rst>
	Computer and project setup with Github, Conda, and the Data Store <step2.rst>
	Use Biocontainers to assemble your tools <step3.rst>
	Use Snakemake to build your workflow <step4.rst>
  Use Jupyter to communicate your results <step5.rst>
..
	#### Comment:This tutorial can have multiple pages. The table of contents assumes
	you have an additional page called 'Step One' with content located in 'step1.rst'
	Edit these titles and filenames as needed ####


Prerequisites
-------------

Downloads, access, and services
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*In order to complete this tutorial you will need access to the following services/software*

..
	#### comment: delete any row not needed in this table ####

.. list-table::
    :header-rows: 1

    * - Prerequisite
      - Preparation/Notes
      - Link/Download
    * - CyVerse account
      - You will need a CyVerse account to complete this exercise
      - |CyVerse User Portal|
    * - Atmosphere access
      - You must have access to Atmosphere
      - |CyVerse User Portal|

Platform(s)
~~~~~~~~~~~

*We will use the following CyVerse platform(s):*

 ..
   #### comment: delete any row not needed in this table ####

.. list-table::
    :header-rows: 1

    * - Platform
      - Interface
      - Link
      - Platform Documentation
      - Quick Start
    * - Data Store
      - GUI/Command line
      - |Data Store|
      - |Data Store Manual|
      - |Data Store Guide|
    * - Discovery Environment
      - Web/Point-and-click
      - |Discovery Environment|
      - |DE Manual|
      - |Discovery Environment Guide|
    * - Atmosphere
      - Command line (ssh) and/or Desktop (VNC)
      - |Atmosphere|
      - |Atmosphere Manual|
      - |Atmosphere Guide|


Application(s) used
~~~~~~~~~~~~~~~~~~~
..
	#### Comment: these tables are examples, delete whatever is unnecessary ####

**Atmosphere Image(s):**

.. list-table::
    :header-rows: 1

    * - Image name
      - Version
      - Description
      - Link
      - Notes/other links
    * - Ubuntu 18.04 NoDesktop Base
      - 2.0
      - Base image with Ubuntu 18.04
      - |Atmosphere Image|
      - Large2 size recommended

**Other Installations**

    .. Note::

						 In most cases, we will be installing these tools on Atmosphere
						 instances. This tutorial is mostly command line. If using your own
						 computer you will need a terminal already installed on Linux or
						 mac). If you are using Windows, please see the instructions for
						 installing the |Windows Linux Subsystem|.

.. list-table::
    :header-rows: 1

    * - Prerequisite
      - Notes
      - Links
    * - Docker
      - Docker is a technology for running individual software tools in a
        reproducible environment, and on any machine. We request you install
        The **Desktop, Community Edition** of Docker if you are using
        Windows or MacOS. The **Docker CE x86_64** instructions are also
        available for Linux users. If you have installation problems, we
        will also have backup cloud instances available.
      - |Download Docker|
    * - Git
      - Git is a version control software.
        If you are using Linux or MacOS, you most likely already have Git.
        Windows users may need to install Git. You can find instructions
        for Git installation at the link.
      - |Install Git|
    * - Text editor
      - You will need to have a text editor suitable for working with code
        (*Not* Microsoft word, or other word processing software).
      - We recommend |Atom|
    * - Jupyter lab
      - Jupyter is a popular data science workbench for reproducible analysis
      - |Install Juypyter lab|
    * - Windows Linux Subsystem (Windows PC only)
      - This enables using Linux on Windows 10 PCs
      - We suggested installing the |Windows Linux Subsystem| if you have
        a Windows 10 PC. Following the directions we also suggest
        installing  an Ubuntu 18.04 LTS distribution.

Input and example data
~~~~~~~~~~~~~~~~~~~~~~

In this tutorial, we will be assembling the genome of the protozoan
Plasmodium falciparum - the parasite that causes malaria. We will be
importing data from the NCBI Sequence Read Archive.


.. list-table::
    :header-rows: 1

    * - Input File(s)
      - Format
      - Preparation/Notes
      - Example Data
    * - NCBI Sequence read data
      - NCBI SRA archival file
      - Files will be imported using NCBI SRA tools
      - |NCBI Experiment Description|

----

**Fix or improve this documentation**

Search for an answer:
|CyVerse Learning Center|


----

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`__


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

.. |Download Cyberduck| raw:: html

   <a href="https://cyberduck.io/" target="blank">Download Cyberduck</a>

.. |Atmosphere Image|  raw:: html

   <a href="https://atmo.cyverse.org/application/images/1552" target="blank">Atmosphere Image</a>

.. |NCBI Experiment Description|  raw:: html

   <a href="https://www.ncbi.nlm.nih.gov/bioproject/PRJNA494861" target="blank">NCBI Experiment Description</a>
