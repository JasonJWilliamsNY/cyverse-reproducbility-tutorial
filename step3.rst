.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_


Use Biocontainers to assemble your tools
-------------

**Description:**

In this section, we will introduce |Biocontainers| as an easy way to use
bioinformatics tools and their dependancies

  .. admonition:: Discussion - Conda vs. containers

     Why and when should we use Conda or containers?


We suggest completing the |Creating and Running Docker Containers| tutorial
before this section, especially if you have not used containers before.

Pipeline Description
----------------------

You will have to have some rationale for which tools you use and why. For
our tutorial example we will perform the following steps with the
following containers:

.. list-table::
    :header-rows: 1

    * - Step
      - Description
      - Tool
      - Container/docker pull command
    * - Get data from SRA
      - In this tutorial we won't be using data we produced, but
        using data deposited at the NCBI SRA.
      - |sra tools|
      - `docker pull quay.io/biocontainers/sra-tools:2.10.0--pl526he1b5a44_0`
    * - Convert SRA to fastq
      - Files imported from SRA need to be written to the fastq format for
        most downstream analysis
      - Same as `sra-tools`
      - Same as `sra-tools`
    * - Sequence QC
      - We need to check the quality of the read data and trim/filter as needed
      - |fastp|
      - `docker pull biocontainers/fastp:v0.19.6dfsg-1-deb_cv1`
    * - Read assembly
      - We will do a de novo assembly of the reads
      - |SPAdes|
      - `docker pull biocontainers/spades:v3.13.0dfsg2-2-deb_cv1`
    * - Assembly QC
      - We will check the quality of the assembly and visualize our results
      - `docker pull quay.io/biocontainers/quast:5.0.2--py27pl526ha92aebf_0`


Exploring containers with Biocontainers
----------------------------------------

Once you have identified the container of interest, you need to explore how to
run the container and the tool itself. For example,let's build a two-step
workflow with some sample data. Before we move ahead with out own workflow,
we willtry to check the quality of a fastq file with `fastqc` and then use
trimmomatic to trim the file.

1. We can get a small fastq file from the CyVerse data store:

   .. code-block:: bash

     mkdir /scratch/test-workflow

     cd /scratch/test-workflow

     iget -P /iplant/home/shared/cyverse_training/datasets/PRJNA79729/fastq_files/SRR064156.fastq.gz .

2. We can run fastqc on this file using a container from Biocontainers

   .. code-block:: bash

     docker run quay.io/biocontainers/fastqc:0.11.7--4 fastqc SRR064156.fastq.gz

This produces an error:

    .. code-block:: bash

      "Skipping 'SRR064156.fastq.gz' which didn't exist, or couldn't be read"

So, we have to go through the (often long) process of getting our individual
tools to work before we can assemble them into a pipeline.

The problem is that the docker container can not "see" the input file; we need
to become more familiar with the Docker |run command|. We would then learn that
we need to mount our local (Atmosphere) disk so that the file can be seen:

3. Rerun the docker command using the `-v` option.

   .. code-block:: bash

     #mount atmosphere directory /scratch/test-workflow
     #to a directory /work which will be created in the container
     docker run -v /scratch/test-workflow/:/work quay.io/biocontainers/fastqc:0.11.7--4 fastqc /work/SRR064156.fastq.gz

4. Try running fastqc on the sample fastq file above using another version
   available on biocontainers.


Combining Biocontainers in a bash script
----------------------------------------

Ideally, we want to automate how we handle data. One way to automatically
have our applications work is to develop a scrip. Let's add one more tool
(trimmomatic) to our example workflow before doing the main workflow for
the tutorial. You can use the |trimmomatic| manual to determine how the Docker
command should work.

    .. admonition:: Question
       :class: admonition-question

       Using the trimmomatic container below, make a docker command that
       trims the single read using a sliding window of 4 bases and
       trimming when average quality is less than 30 (phred score).
       Use up 8 threads and single-end mode.

       `quay.io/biocontainers/trimmomatic:0.39--1`

       .. admonition:: Answer

          .. code-block:: bash

            docker run -v /scratch/test-workflow/:/work quay.io/biocontainers/trimmomatic:0.39--1 trimmomatic SE -threads 8 /work/SRR064156.fastq.gz /work/SRR064156_trimmed.fastq.gz SLIDINGWINDOW:4:30


Once you have the commands you want to use, you could write a |bash script| to
automate the use of these tools. For more on writing bash scripts see the
|Data Carpentry Genomics Lesson|.

A script could look something like this

     .. code-block:: bash

          #!/bin/bash

          # Make a directory and stage our data

          mkdir -p /scratch/example-script/data
          DATADIRECTORY=/scratch/example-script/data

          #Import data from CyVerse data store
          iget -P /iplant/home/shared/cyverse_training/datasets/PRJNA79729/fastq_files/SRR064156.fastq.gz $DATADIRECTORY

          #Make a directory for our analysis
          mkdir -p /scratch/example-script/analyses
          ANALYSISDIR=/scratch/example-script/analyses

          #Use Docker container to do fastqc
          docker run -v $DATADIRECTORY:/work quay.io/biocontainers/fastqc:0.11.7--4 fastqc /work/SRR064156.fastq.gz

          #move results to analyses directory
          mkdir -p $ANALYSISDIR/fastqc
          mv $DATADIRECTORY/*fastqc* $ANALYSISDIR/fastqc

          #Use Docker container to do trimmomatic
          docker run -v $DATADIRECTORY:/work quay.io/biocontainers/trimmomatic:0.39--1 trimmomatic SE -threads 8 /work/SRR064156.fastq.gz /work/SRR064156_trimmed.fastq.gz SLIDINGWINDOW:4:30

          #move results to analyses directory
          mkdir -p $ANALYSISDIR/trimmomatic
          mv $DATADIRECTORY/*_trimmed.fastq.gz $ANALYSISDIR/trimmomatic


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


.. |Biocontainers|  raw:: html

   <a href="https://biocontainers.pro/" target="blank">Biocontainers</a>

.. |Creating and Running Docker Containers|  raw:: html

   <a href="https://cyverse-creating-docker-containers-quickstart.readthedocs-hosted.com/en/latest/" target="blank">Creating and Running Docker Containers</a>

.. |sra tools|  raw:: html

   <a href="https://ncbi.github.io/sra-tools/" target="blank">sra tools</a>

.. |fastp|  raw:: html

   <a href="https://github.com/OpenGene/fastp" target="blank">fastp</a>

.. |SPAdes|  raw:: html

   <a href="http://cab.spbu.ru/software/spades/" target="blank">SPAdes</a>

.. |QUAST|  raw:: html

   <a href="http://quast.bioinf.spbau.ru/" target="blank">QUAST</a>

.. |run command|  raw:: html

   <a href="https://docs.docker.com/engine/reference/commandline/run/" target="run command">LINK</a>

.. |trimmomatic|  raw:: html

   <a href="http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf" target="blank">trimmomatic</a>

.. |bash script|  raw:: html

   <a href="https://www.linux.com/tutorials/writing-simple-bash-script/" target="blank">bash script</a>

.. |Data Carpentry Genomics Lesson|  raw:: html

   <a href="https://datacarpentry.org/wrangling-genomics/05-automation/index.html" target="blank">Data Carpentry Genomics Lesson</a>
