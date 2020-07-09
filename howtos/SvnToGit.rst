Migrating from SVN to git
======================================
Possibilities
-------------
git-svn
^^^^^^^
:code:`git-svn` is a subversion bridge and well `documented <https://git-scm.com/docs/git-svn>`_ within that versioning control system. 

svn2git
^^^^^^^
:code:`svn2git` is much more comfortable to use, but aparently takes a directory structure as given, which does not exist for 
ADE-developed software modules at Gemini. Hence, this tool cannot be used for this purpose.

Software Requirements
---------------------
Operating System
^^^^^^^^^^^^^^^^
The following procedure assumes CentOS7 as operating system for the migration steps. Principally, things should be working 
with other linux distributions or even operating systems as well, but to prevent against svn or git version conflicts the 
steps are taken under the mentioned one.

git
^^^
Of course, an installation of :code:`git` is needed to migrate to it. This is done easily with

::
  
  sudo yum install -y git git-svn
  
svn
^^^
On a typical gemini development machine, :code:`subversion` should already be installed. If not, install it using the equivalent to the command above

::

  sudo yum install -y subversion
  
Migration Procedure
-------------------
In the following, the `crcs` project will be used as example to demonstrate a possible procedure for the repository migration. Also, being a module from the
:code:`ioc` branch of the ADE generation 1.x, this also points to the problems regarding the site divergence and the current repository structure under 
:code:`svn`. Migrating to git should make a future site code unification possible, but the history for both sites of a subsystem needs to be kept and ported
into git. How to do this, still needs to be discussed in the future, for the time being, projects like this will be mnigrated using a site specific suffix.

Authors File Creation
^^^^^^^^^^^^^^^^^^^^^
First, a authors file needs to be created from the svn commits:

::
  
  svn log -q http://sbfsvn02/gemini-sw/gem/trunk | awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > authors.txt
  
This creates a file :code:`authors.txt` in the current directory and should be edited that it contains authors with their email adresses:

::

  jdoe = John Doe <jdoe@example.com>
  jeod = Jane Eod <jeod@example.com>

This file can be used for all the other projects that need to be migrated, too. Missing users have to be added over time using this technique. 

.. note:: This only needs to be done once. You can also find this file at the top level in Gemini's gitlab repository authors project.

Find First Commit
^^^^^^^^^^^^^^^^^
THe next step is to find the revision number in the subversion repository, when the project was created (i.e. the initial commit). Because
initial steps are taken on the :code:`trunk` (and not in ):

::

  svn log --stop-on-copy http://sbfsvn02/gemini-sw/gem/trunk/ioc/gpol
  
The end of the output for this example looks like this:

::
  
  [...]
  New import of crcs-mk from crcs-cp
  ------------------------------------------------------------------------
  r2068 | jdoe | 2017-07-20 07:17:13 +0200 (Thu, 20 Jul 2017) | 1 line
  
  New ignore
  ------------------------------------------------------------------------
  r2067 | jdoe | 2017-07-20 06:57:23 +0200 (Thu, 20 Jul 2017) | 1 line
  
  crcs/mk: changed contact and set svn:ignore
  ------------------------------------------------------------------------
  r2066 | jdoe | 2017-07-20 06:57:05 +0200 (Thu, 20 Jul 2017) | 1 line
  
  Initial structure of new crcs/mk
  ------------------------------------------------------------------------


So obviously, the revision number wanted in this case is r2066.

Convert SVN project to git
^^^^^^^^^^^^^^^^^^^^^^^^^^
Now, a state is reached where the :code:`svn` project can be migrated to :code:`git`. It needs to be understood, that Gemini uses a non-standard
repository layout for subversion. All projects are organized under only one :code:`trunk`, :code:`tags` (:code:`release` at Gemini) and :code:`branches`
directory. Hence, the project specific paths need to be detailed with according parameters to the :code:`git svn clone` command:

::

    git svn clone -r2066:HEAD --preserve-empty-dirs  --trunk=trunk/ioc/crcs/mk --tags=release/ioc/crcs/mk --branches=branches/ioc/crcs/mk --authors-file=authors.txt http://sbfsvn02/gemini-sw/gem/ crcs_mk-tmp
    cd crcs_mk-tmp

.. caution:: If this command does not succeed, it needs to be made sure, that the target directory containing the temporary :code:`git` project 
 (:code:`crcs_mk-tmp` in this example) is deleted, before trying another run!

Create :code:`.gitignore` file(s)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are two possibilities to create :code:`.gitignore`.

1. Recursively create a :code:`.gitignore` file in each subdirectory, where a :code:`svn:ignore` property is specified:

::
    
    git svn create-ignore

2. Create one :code:`.gitignore` file for the whole project:

::
    
    git svn show-ignore > .gitignore


Convert SVN-tag-branches to git tags
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The release names from subversion were migrated to :code:`tags/name`. The outcome of the preceding steps for this example looks like:

::

    $ git branch -a
	* master
  	remotes/R314
  	remotes/tags/2-0
  	remotes/tags/2-1-BR314
  	remotes/tags/2-10
  	remotes/tags/2-11
  	remotes/tags/2-12
  	remotes/tags/2-13
  	remotes/tags/2-2-BR314
  	remotes/tags/2-3-BR314
  	remotes/tags/2-4-BR314
  	remotes/tags/2-5-BR314
  	remotes/tags/2-6
  	remotes/tags/2-7
  	remotes/tags/2-8
  	remotes/tags/2-9
  	remotes/trunk

The tags-branches need to be migrated to normal git tags. This is done in one step with the following command:

::

    git for-each-ref --format='%(refname)' refs/remotes/tags | cut -d / -f 4 | while read ref; do git tag -a "$ref" -m "Convert "$ref" to a proper git tag." "refs/remotes/tags/$ref"; git branch -r -D "tags/$ref"; done

The outcome should look like:

::
    
    $ git branch -a
    * master
    remotes/R314
    remotes/trunk

    $ git tag
    2-0
    2-1-BR314
    2-10
    2-11
    2-12
    2-13
    2-2-BR314
    2-3-BR314
    2-4-BR314
    2-5-BR314
    2-6
    2-7
    2-8
    2-9

Delete trunk
^^^^^^^^^^^^
Since :code:`trunk` was automatically migrated to master already by :code:`git svn clone` it can be deleted:

::

    git branch -r -d trunk


The outcome should look like:

::
    
    $ git branch -a
    * master
    remotes/R314

Convert SVN-branches to local branches
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The remaining branches - after converting the tags-branches to git tags and after deleting the trunk-branch - now need to be converted to 
local git branches. This is done similar to the svn-git-branches conversion:

::

    git for-each-ref --format='%(refname)' refs/remotes | cut -d / -f 3 | while read ref; do git branch --track "$ref" "$ref"; git branch -r -D "$ref"; done


The outcome should look like:

::

    $ git branch -a
    R314
    * master


Add remote and push everything
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Now an existing remote :code:`git` URL could to be added as origin:

::

    git remote add origin https://gitlab.gemini.edu/rtsw/ioc/crcs_mk.git
    git push --all
    git push --tags

Alternatively, if the remote does not yet exist, it could be created in the same step:

::
    
    git push --all --set-upstream https://gitlab.gemini.edu/rtsw/ioc/crcs_mk.git 
    git push --tags

Final test
^^^^^^^^^^
To test the outcome finally, delete the :code:`crcs_mk-tmp` directory and clone the project that was just created from the git repository:

::

    cd ..
    rm -rf crcs_mk-tmp
    git clone https://gitlab.gemini.edu/rtsw/ioc/crcs_mk.git
    cd crcs_mk

The outcome should look like:    

::

    $ git branch -a
    * master
    remotes/origin/HEAD -> origin/master
    remotes/origin/R314
    remotes/origin/master
