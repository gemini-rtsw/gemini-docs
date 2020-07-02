Quick Start Guide
=================

Convert docs to rst or write rst
--------------------------------

*Read the docs uses RestructuredText (rst) markup text. All documents must be written in rst.* 

 1. Install pandoc then ``pandoc --help`` 
 2. pdfs will have to be converted to docx using Word, most other formats can be converted directly
 3. ``pandoc -f docx -t rst filename.docx -o filename.rst`` typical pandoc usage
 4. ``pandoc -f docx -t rst filename.docx -o filename.rst --extract-media=./media`` convert doc with images
 5. Or `write rst <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_
 

 * Note: rst is indent aware like python 

Put docs into the Gemini Documents repo 
---------------------------------------

 1. Install git 
 2. ``git clone git@github.com:gemini-rtsw/gemini-docs.git`` clone the Gemini Docs repo
 3. Copy rst files into the appropriate repo directory
 4. ``git add <filename>`` add your new files to the repo
 5. ``vi index.rst`` edit the appropriate index.rst file and add your files to a toctree. See the index.rst below. ::

   .. literalinclude:: ../index.rst 
      :language: rst
      :emphasize-lines: 10 

 6. ``git commit <filename[s]> -m "comment"`` commit your changes
 7. ``git push`` push changest to github and trigger Read-The-Docs build
 8. After the ``git push`` Read-The-Docs will take a few minutes to upload your docs
 9. View updated docs `gemini-docs-rt.readthedocs.io <https://gemini-docs-rt.readthedocs.io/en/latest/index.html>`_
 10. View the build page 

  * The `github.com <https://www.github.com/gemini-rtsw/gemini-docs>`_ repo page will also have a link to the documents on Read-The-Docs

Compile rst locally (highly recommended)
--------------------------
 
 1. Install pip (Ex: on macOS ``sudo easy_install pip``)
 2. ``pip install sphinx``
 3. ``pip install sphinx_rtd_theme``
 4. ``sphinx-build --version`` 
 5. On macOS only, add ``PATH="$HOME/Library/Python/2.7/bin:$PATH"`` to .bash_profile 
 6. ``make html`` in Gemini Docs repository home
 7. Your files will be in ``_build/html``

Create a Read-The-Docs account (optional)
-----------------------------------------

 1. Go to `Read-The-Docs <readthedocs.org>`_  
 2. Click on `Sign up`
 3. Click on `Sign up with GitHub`
 4. Click on `Import a Project`
 5. From your main page you can click on a project to access the admin page  
 

Additional Info
---------------

 * `reStructuredText Primer <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_

 * `TOC Tree usage <https://www.sphinx-doc.org/en/1.5/markup/toctree.html>`_ 
 
 * `Another rst guide <https://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.htmltext#-formatting>`_ 


