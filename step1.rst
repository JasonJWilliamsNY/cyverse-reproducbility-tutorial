.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_


Intro and Github setup
-------------------------

**Description:**

In this tutorial, we will assemble the genome of |Plasmodium falciparum| - the
parasite that causes malaria. We will be importing data from the NCBI Sequence
Read Archive. There are a variety of science and reproducibility goals that
we will attempt during the tutorial:

*Scientific Goals*

 - Importing data from the |NCBI Sequence Read Archive|
 - Checking and filtering sequences reads by quality using |fastp|
 - Assembling a small genome using |SPAdes|
 - Checking the quality of the assembly and visualizing results using |QUAST|

*Reproducibility Goals*

 - Developing code using version control on |GitHub|
 - Setting up tools using |conda| on the |Atmosphere| cloud
 - Assembling tools using |Biocontainers|
 - Building a workflow using |Snakemake|
 - Reporting on results using |JupyterLab|

 .. note::

    This tutorial is under active development  - a few **caveats**
    and **prerequisites**. Currently this tutorial is designed to
    be used in a workshop with an experienced instructor and is not
    yet at the level of detail for a novice learner to get through
    all the materials on their own.

    1. The goal is to demonstrate how certain tools *could* be used
       to enhance the reproducibility of your analyses. The focus is
       not the science, so this tutorial is not a substitute for a
       thorough explanation of a genome assembly.
    2. This tutorial requires some substantial background knowledge
       including:
        - Knowledge of the linux command line
        - Some knowledge of Git
        - Some knowledge of bioinformatics tools

       If you are new to these areas, you can still benefit, but be
       sure to visit the |Github Repo Link| and find the **Issues** link:
       leave us a comment about things you did not understand or
       needed more help with. Where appropriate we will link to other
       tutorials that provide some of this background knowledge.


----

*Choose a License*
~~~~~~~~~~~~~~~~~~~

Following the |4OSS| recommendations, we should develop our code openly from
day one. If you a single line of code for your project, you are a developer
and have a responsibility to scientific ethics, your laboratory, your
colleagues, and yourself.

We cannot make specific recommendations about what license you should choose.
We suggest checking your laboratory data management plans/policies and or
consulting with your University/Institution on their requirements. |Choosealicense|
and |Creative Commons| have some recommendations on licensing.

1. Go to |Choosealicense| and |Creative Commons| and choose an appropriate
   license.

   For this tutorial we suggest the |GNU GPLv3|

*Setup a Github Repository*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Next, we will create a repository for any scripts, notebooks, etc. that will be
a part of our project. To learn more about Github and git, see the
|Software Carpentry Git Lesson| and our |FOSS Camp Git|


1. Go to |Github| and sign into your account.

2. On the upper-right of your GitHub page you should find a "+" link;
   from this menu select "New repository"

3. Name your repository (e.g. **reproducibility-tutorial**) and git it a
   description.

4. Select "Initialize this repository with a README" and also choose
   a license. (e.g. GNU General Public License v3.0)

5. Click :guilabel:`&Create Repository` to create your repository.

   .. tip::

      Although we want encourage open source, you do not need to make
      the repository public at this time. You can have one private
      repository in your GitHub free account.

  .. warning::

      Never "commit" any sensitive information (e.g. logins, passwords,
      private keys, protected data) in your GitHub repository. Git and
      Github were designed to make it possible to go back to previous
      versions of documents/files so even if you delete a mistake, there
      is a history which may allow someone else to see past commits.

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

.. |NCBI Sequence Read Archive|  raw:: html

   <a href="https://www.ncbi.nlm.nih.gov/sra" target="blank">NCBI Sequence Read Archive</a>

.. |fastp|  raw:: html

   <a href="https://github.com/OpenGene/fastp" target="blank">fastp</a>

.. |SPAdes|  raw:: html

   <a href="http://cab.spbu.ru/software/spades/" target="blank">SPAdes</a>

.. |GitHub|  raw:: html

   <a href="https://github.com/" target="blank">GitHub</a>

.. |QUAST|  raw:: html

   <a href="http://quast.bioinf.spbau.ru/" target="blank">QUAST</a>

.. |conda|  raw:: html

   <a href="https://docs.conda.io/en/latest/" target="blank">conda</a>

.. |Biocontainers|  raw:: html

   <a href="https://biocontainers.pro/" target="blank">Biocontainers</a>

.. |Snakemake|  raw:: html

   <a href="https://snakemake.readthedocs.io/en/stable/" target="blank">Snakemake</a>

.. |JupyterLab|  raw:: html

   <a href="https://jupyterlab.readthedocs.io/en/stable/" target="blank">JupyterLab</a>

.. |Choosealicense| raw:: html

   <a href="https://choosealicense.com/" target="blank">Choosealicense</a>

.. |4OSS|  raw:: html

   <a href="https://softdev4research.github.io/recommendations/" target="blank">4OSS</a>

.. |Creative Commons|  raw:: html

   <a href="https://creativecommons.org/share-your-work/" target="blank">Creative Commons</a>

.. |GNU GPLv3|  raw:: html

   <a href="https://choosealicense.com/licenses/gpl-3.0/" target="blank">GNU GPLv3</a>

.. |Plasmodium falciparum|  raw:: html

   <a href="https://en.wikipedia.org/wiki/Plasmodium_falciparum" target="blank">Plasmodium falciparum</a>

.. |Software Carpentry Git Lesson|  raw:: html

   <a href="http://swcarpentry.github.io/git-novice/" target="blank">Software Carpentry Git Lesson</a>

.. |FOSS Camp Git|  raw:: html

   <a href="https://learning.cyverse.org/projects/foss-2020/en/latest/reproducible_science/version_control.html" target="blank">FOSS Camp Git</a>
