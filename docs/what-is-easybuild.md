# What is EasyBuild?

!!! warning
    This page will soon replace <https://docs.easybuild.io/en/latest/Introduction.html>.

    **
    It still needs to be ported from *reStructuredText* (.rst) to *MarkDown* (.md),  
    and you can help with that!
    **

    - source: [`docs/Introduction.rst` in `easybuilders/easybuild` repo](https://raw.githubusercontent.com/easybuilders/easybuild/develop/docs/Introduction.rst)
    - target: [`docs/what-is-easybuild.md` in `easybuilders/easybuild-docs` repo](https://github.com/easybuilders/easybuild-docs/tree/main/docs/what-is-easybuild.md)

    See <https://github.com/easybuilders/easybuild-docs> for more information.

```rst
.. _what_is_easybuild:

What is EasyBuild?
------------------

EasyBuild is a software build and installation framework that allows you to manage (scientific) software on High 
Performance Computing (HPC) systems in an efficient way. It is motivated by the need for a tool that combines the
following features: 

* a **flexible framework** for building/installing (scientific) software
* fully **automates** software builds
* divert from the standard ``configure`` / ``make`` / ``make install`` with custom procedures
* allows for easily **reproducing** previous builds
* keep the software build recipes/specifications **simple and human-readable**
* supports **co-existence of versions/builds** via dedicated installation prefix and module files
* enables **sharing** with the HPC community (win-win situation)
* automagic **dependency resolution**
* **retain logs** for traceability of the build processes

Some key features of EasyBuild:

* build & install (scientific) software **fully autonomously**

  * also interactive installers, code patching, generating module file, ...

* easily :ref:`configurable <configuring_easybuild>`: config file/environment/command line

  * including aspects like module naming scheme

* thorough logging and archiving (see :doc:`Logfiles`)

  * entire build process is logged thoroughly, logs are stored in install directory;
  * easyconfig file used for build is archived (install directory + file/svn/git repo) 

* automatic **dependency resolution** (see :ref:`use_robot`)

  * build entire software stack with a single command, using ``--robot``

* building software in **parallel**
* robust and thoroughly tested code base, fully unit-tested before each release
* thriving, growing **community**

Take a look at our HUST'14 workshop paper
`Modern Scientific Software Management Using EasyBuild and Lmod`
(`download PDF here <https://easybuilders.github.io/easybuild/files/hust14_paper.pdf>`_)
and use that as a reference in case you present academic work mentioning EasyBuild.

```
