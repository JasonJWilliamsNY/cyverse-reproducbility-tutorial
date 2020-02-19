.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_


Use Snakemake to build your workflow
---------------------------------------

**Description:**

In this section we will build our intended workflow using |Snakemake|.

Why use Snakemake? There are a lot of features that give us more
control over our workflow than a bash script:

- It is FOSS (Free, open-source software)
- It can run on Windows/MacOS/Linux and works with HPC
- It is Python-based and you can use Python in your code.
- If you are familiar with |make| you understand the basic way it Snakemake
  organizes things.



*Snakemake example*
~~~~~~~~~~~~~~~~~~~~~~~

Snakemake uses metaphors like rules and recipes to describe how a workflow is
organized. A **rule** states what output should be created and defines what
steps (the recipe) needs to be follow to create that output. Let's work on
the first step of our workflow.

**Import a sequence from SRA**

1. We will import a SRA file, so the first thing we do is create a rule that states
   what output we should end up with. In our tutorial, we will be working from
   a single set of reads from the Plasmodium data:

   .. code-block:: python

       rule import_from_sra:
         output:
            "SRR8245081.sra"

2. We have to build on this by specifying some shell command that will create
   this. We will use a docker container:

   .. code-block:: python

       rule import_from_sra:
           output:
              "/scratch/learn-snake/sra_files/SRR8245081/SRR8245081.sra"
           shell:
         # Use a docker run command and mount a director
              "docker run -v /scratch/learn-snake/:/experiment/ "
         # set a working directory - this will be created if needed
              "--workdir /experiment/sra_files "
         # set the docker container
              "quay.io/biocontainers/sra-tools:2.10.0--pl526he1b5a44_0 "
         # use the prefetch tool
              "prefetch "
         # show progress
              "-p 1 "
         # get our desired accession
              "SRR8245081 "
         # output to our desired directory
              "--output-directory /experiment/sra_files/"

  .. tip::

    Use a text editor to compose your Snakefile as python will be
    sensitive to indenting.


3. Once we have figured out the correct set of commands, we write the code
   in a file usually called a `Snakefile`. We then run the Snakefile.

   .. code-block:: bash

     # This assumes you are in a directory with
     # a file `Snakefile`
     snakemake -s Snakefile


When run we get some helpful outputs:

    .. code-block:: bash

      Building DAG of jobs...
      Using shell: /bin/bash
      Provided cores: 1 (use --cores to define parallelism)
      Rules claiming more threads will be scaled down.
      Job counts:
	       count	jobs
	       1	import_from_sra
	       1

      [Thu Dec  5 04:16:50 2019]
      rule import_from_sra:
        output: /scratch/learn-snake/sra-files/SRR8245081.sra
        jobid: 0

        ...

      [Thu Dec  5 04:17:03 2019]
      Finished job 0.
      1 of 1 steps (100%) done
      Complete log: /scratch/learn-snake/.snakemake/log/2019-12-05T042511.996668.snakemake.log

There is information about how (or if) this job is parallelized,
what job(s) will be run, and what is the expected output. Importantly,
if the expected outputs are not created, Snakemake will inform you.

We can also see a diagram of the workflow visualized

     .. code-block:: bash

       #install a needed dependancy
       sudo apt-get install -y graphviz

       #make a PNG of your workflow as a "directed acyclic graph"
       snakemake --dag | dot -Tpng > dag.png

You can use scp to copy the file to your computer.

      .. code-block:: bash

       # remember to use your username and Atmosphere IP
       scp YOURCYVERSEUSERNAME@123.45.67:/scratch/learn-snake/dag.png ~/Desktop

Use scp to copy this. It's not that impressive, but will be as we build our
workflow.

   .. tip::

     To build your own Snakemake workflow familiarize yourself with
     |Snakemake grammar|.

*Full Snakemake worfklow*
~~~~~~~~~~~~~~~~~~~~~~~~~~

First, let's set up our workflow in our tutorial folder

1. Create an snakemake folder in our tutorial folder

   .. code-block:: bash

      mkdir -p /scratch/reproducibility-tutorial/snakemake


Here is a much more advanced, fully assembled workflow. We will walk through
and discuss the key features:

   .. code-block:: python

    #SRA definitions
    ACCESSION=["SRR8245081",]
    READS=["_1","_2"]
    REFERENCE_URLS=["ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/002/765/GCF_000002765.4_ASM276v2/GCF_000002765.4_ASM276v2_genomic.fna.gz",
                    "ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/002/765/GCF_000002765.4_ASM276v2/GCF_000002765.4_ASM276v2_genomic.gff.gz"]
    REFERENCES_BASE=["GCF_000002765.4_ASM276v2_genomic",]

    #containers
    SRA_TOOLS_CONTAINER="quay.io/biocontainers/sra-tools:2.10.0--pl526he1b5a44_0"
    FASTP_CONTAINER="biocontainers/fastp:v0.19.6dfsg-1-deb_cv1"
    SPADES_CONTAINER="biocontainers/spades:v3.13.0dfsg2-2-deb_cv1"
    QUAST_CONTAINER="quay.io/biocontainers/quast:5.0.2--py27pl526ha92aebf_0"

    rule all:
        input:
            sra=expand("/scratch/reproducibility-tutorial/experiment/sra_files/{accession}/{accession}.sra",
                    accession=ACCESSION),
            fastq=expand("/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra{reads}.fastq",
                    accession=ACCESSION,
                    reads=READS),
            fastq_trimmed=expand("/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra{reads}_trimmed.fastq",
                    accession=ACCESSION,
                    reads=READS),
            fastp_report=expand("/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}_fastp_report.html",
                    accession=ACCESSION),
            assembly_contigs=expand("/scratch/reproducibility-tutorial/experiment/{accession}_assembly/contigs.fasta",
                    accession=ACCESSION),
            reference_genome=expand("/scratch/reproducibility-tutorial/experiment/reference_genome/{reference_genome_fna}.fna",
                    reference_genome_fna=REFERENCES_BASE),
            reference_annotation=expand("/scratch/reproducibility-tutorial/experiment/reference_genome/{reference_genome_gff}.gff",
                    reference_genome_gff=REFERENCES_BASE),
            quast_assembly_report=expand("/scratch/reproducibility-tutorial/experiment/{accession}_assembly_stats/report.html",
                    accession=ACCESSION)



    rule SRA_Import:
        output:
            "/scratch/reproducibility-tutorial/experiment/sra_files/{accession}/{accession}.sra"
        params:
            container={SRA_TOOLS_CONTAINER},
            command="prefetch",
            user="root",
            progress=1,
            outputdirectory="/experiment/sra_files"
        shell:
            "docker run --user {params.user} "
            " -v /scratch/reproducibility-tutorial/experiment:/experiment "
            "--workdir /experiment "
            "{params.container} {params.command} "
            "-p {params.progress} "
            "--output-directory {params.outputdirectory} "
            "{ACCESSION}"

    rule SRA_to_fastq:
        input:
            sra=expand("/scratch/reproducibility-tutorial/experiment/sra_files/{accession}/{accession}.sra",
                    accession=ACCESSION)
        output:
            "/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra_1.fastq",
            "/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra_2.fastq"
        params:
            container={SRA_TOOLS_CONTAINER},
            command="fasterq-dump",
            user="root",
            outputdirectory="/experiment/fastq_files"
        threads:
            8
        shell:
            "docker run --user {params.user} "
            " -v /scratch/reproducibility-tutorial/experiment:/experiment "
            "--workdir /experiment/sra_files/{ACCESSION}/ "
            "{params.container} {params.command} "
            "{ACCESSION}.sra "
            "-O {params.outputdirectory} "

    rule fastq_qc:
        input:
            fastq=expand("/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra{reads}.fastq",
                accession=ACCESSION,
                reads=READS)
        output:
            "/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra_1_trimmed.fastq",
            "/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra_2_trimmed.fastq",
            "/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}_fastp_report.html"
        params:
            container={FASTP_CONTAINER},
            command="fastp",
            user="root",
            outputdirectory="/experiment/fastq_files"
        threads:
            8
        shell:
            "docker run --user {params.user} "
            " -v /scratch/reproducibility-tutorial/experiment:/experiment "
            "--workdir /experiment/fastq_files/ "
            "{params.container} {params.command} "
            "-V "
            "--cut_front "
            "--cut_front_mean_quality 30 "
            "- R '{ACCESSION} fastp report' "
            "--html {ACCESSION}_fastp_report.html "
            "-i /experiment/fastq_files/{ACCESSION}.sra_1.fastq "
            "-o /experiment/fastq_files/{ACCESSION}.sra_1_trimmed.fastq "
            "-I /experiment/fastq_files/{ACCESSION}.sra_2.fastq "
            "-O /experiment/fastq_files/{ACCESSION}.sra_2_trimmed.fastq"

    rule SPAdes_assembly:
        input:
            fastq_trimmed=expand("/scratch/reproducibility-tutorial/experiment/fastq_files/{accession}.sra{reads}_trimmed.fastq",
                accession=ACCESSION,
                reads=READS)
        output:
            "/scratch/reproducibility-tutorial/experiment/{accession}_assembly/contigs.fasta"
        params:
            container={SPADES_CONTAINER},
            command="spades.py",
            user="root",
            outputdirectory="/experiment/{accession}_assembly"
        threads:
            8
        shell:
            "docker run --user {params.user} "
            " -v /scratch/reproducibility-tutorial/experiment:/experiment "
            "--workdir /experiment/fastq_files/ "
            "{params.container} {params.command} "
            "-1 /experiment/fastq_files/{ACCESSION}.sra_1_trimmed.fastq "
            "-2 /experiment/fastq_files/{ACCESSION}.sra_2_trimmed.fastq "
            "-o {params.outputdirectory}"

    rule import_reference_genome:
        output:
            reference_genome="/scratch/reproducibility-tutorial/experiment/reference_genome/{REFERENCES_BASE}.fna",
            reference_annotation="/scratch/reproducibility-tutorial/experiment/reference_genome/{REFERENCES_BASE}.gff"
        shell:
            "mkdir -p /scratch/reproducibility-tutorial/experiment/reference_genome &&  "
            "cd /scratch/reproducibility-tutorial/experiment/reference_genome && "
            "wget {REFERENCE_URLS} && "
            "gzip -d {REFERENCES_BASE}*.gz"

    rule assembly_stats:
        input:
            assembly_contigs=expand("/scratch/reproducibility-tutorial/experiment/{accession}_assembly/contigs.fasta",
                accession=ACCESSION),
            reference_genome=expand("/scratch/reproducibility-tutorial/experiment/reference_genome/{reference_genome_fna}.fna",
                reference_genome_fna=REFERENCES_BASE),
            reference_annotation=expand("/scratch/reproducibility-tutorial/experiment/reference_genome/{reference_genome_gff}.gff",
                reference_genome_gff=REFERENCES_BASE)
        output:
            "/scratch/reproducibility-tutorial/experiment/{accession}_assembly_stats/report.html"
        params:
            container={QUAST_CONTAINER},
            command="quast.py",
            user="root",
            outputdirectory="/experiment/{accession}_assembly_stats",
            container_input=expand("/experiment/{accession}_assembly/contigs.fasta",
                accession=ACCESSION)
        threads:
            8
        shell:
            "docker run --user {params.user} "
            " -v /scratch/reproducibility-tutorial/experiment:/experiment "
            "--workdir /scratch/reproducibility-tutorial/experiment/reference_genome/ "
            "{params.container} {params.command} "
            "-o {params.outputdirectory} "
            "--features gene:/experiment/reference_genome/{REFERENCES_BASE}.gff "
            "-r /experiment/reference_genome/{REFERENCES_BASE}.fna "
            "-t 8 "
            "--eukaryote "
            "--circos "
            "{params.container_input}"

2. In the snakemake folder you can run this job

   .. code-block:: language

     snakemake --cores all

  .. tip::

     **Use `tmux`**

     Since we happen to be working on the cloud, you may want to
     walk away from an ssh session and have your work run without
     being connected (e.g. you want to close your laptop). You
     can use a tool like `tmux` to do that. See this |tmux tutorial|.


  .. admonition:: Discussion - Parallelism

     What happens when you run Snakemake


3. When you finish, don't forget to generate a diagram of your workflow

  .. code-block:: language

    snakemake --dag | dot -Tpng > dag.png


*Document your work*
~~~~~~~~~~~~~~~~~~~~~~~~~~

1. As previously, you should copy your results to CyVerse and commit
  code changes to GitHub (remember to have a git ignore file so
  you don't track large files)



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


.. |Snakemake|  raw:: html

   <a href="https://snakemake.readthedocs.io/en/stable/" target="blank">Snakemake</a>

.. |make|  raw:: html

   <a href="http://swcarpentry.github.io/make-novice/" target="blank">make</a>

.. |Snakemake grammar|  raw:: html

   <a href="https://snakemake.readthedocs.io/en/stable/snakefiles/writing_snakefiles.html" target="blank">Snakemake grammar</a>

.. |tmux tutorial|  raw:: html

   <a href="https://thoughtbot.com/blog/a-tmux-crash-course" target="blank">tmux tutorial</a>
