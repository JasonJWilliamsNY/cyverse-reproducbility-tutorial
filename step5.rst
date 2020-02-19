.. include:: cyverse_rst_defined_substitutions.txt

|CyVerse logo|_

|Home_Icon|_
`Learning Center Home <http://learning.cyverse.org/>`_


Use Jupyter to communicate your results
-------------

**Description:**

In this final step - how you document is up to you:


*Start Jupyter and begin working through your results*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. You can start your Jupyter lab with the following command

  .. code-block:: bash

    /opt/conda/bin/jupyter lab --no-browser --allow-root --ip=0.0.0.0 --NotebookApp.token='' --NotebookApp.password='' --notebook-dir='/scratch/reproducibility-tutorial/'


  .. admonition:: Exercise

     The 5.10 version of snakemake has integration with Jupyter.
     how can you use this to blend the two? Install the new
     version of snakemake and try, see the |snakemake docs|
     for information on the notebook integration.




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

.. |snakemake docs|  raw:: html

   <a href="https://snakemake.readthedocs.io/en/stable/snakefiles/rules.html#jupyter-notebook-integration" target="blank">snakemake docs</a>
