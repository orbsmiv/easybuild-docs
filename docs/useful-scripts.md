# Useful scripts

!!! warning
    This page will soon replace <https://docs.easybuild.io/en/latest/Useful-scripts.html>.

    **
    It still needs to be ported from *reStructuredText* (.rst) to *MarkDown* (.md),  
    and you can help with that!
    **

    - source: [`docs/Useful-scripts.rst` in `easybuilders/easybuild` repo](https://raw.githubusercontent.com/easybuilders/easybuild/develop/docs/Useful-scripts.rst)
    - target: [`docs/useful-scripts.md` in `easybuilders/easybuild-docs` repo](https://github.com/easybuilders/easybuild-docs/tree/main/docs/useful-scripts.md)

    See <https://github.com/easybuilders/easybuild-docs> for more information.

```rst
.. _useful_scripts:

Useful scripts
==============

A couple of useful stand-alone scripts are provided along with the EasyBuild framework.

The latest stable version of these scripts is available in the ``easybuild-framework`` GitHub repository at
https://github.com/easybuilders/easybuild-framework/tree/main/easybuild/scripts.

This documentation provides some information on how to use these scripts.

.. _fix_broken_easyconfigs_script:

``fix_broken_easyconfigs.py``
-----------------------------

*download from:* https://github.com/easybuilders/easybuild-framework/blob/main/easybuild/scripts/fix_broken_easyconfigs.py

To fix easyconfig files that are broken due to relying on functionality that is no longer supported in EasyBuild v2.x,
the ``fix_broken_easyconfigs.py`` script can be used.

This script rewrites easyconfig files that are broken because they still:

* rely on the automagic fallback to the generic ``ConfigureMake`` easyblock (see :ref:`depr_ConfigureMake_fallback_eb1`)
* define the ``premakeopts`` and/or ``makeopts`` eaysconfig parameters (see :ref:`depr_premakeopts_makeopts_eb1`)
* use the ``shared_lib_ext`` 'constant' (see :ref:`depr_shared_lib_ext_eb1`)
* incorrectly use the ``license`` easyconfig parameter (see :ref:`depr_license_eb1`)

The script accepts a list of easyconfig files or directories containing easyconfig files (``.eb``) as arguments,
and will consider all easyconfig files it can find.

Only easyconfig files that are considered broken (according to one or more of the aspects listed above) are patched;
other easyconfigs will be left untouched.

To determine whether ``easyblock = 'ConfigureMake'`` should be added in an easyconfig file that does
not include any ``easyblock`` specification yet, the easyblocks available in the active Python search path (i.e.,
the ones listed in the output of ``eb --list-easyblocks``, see also :ref:`list_easyblocks`) are taken into account.

A backup copy is created for each easyconfig file that is being patched.

Example usage::

    $ python easybuild/scripts/fix_broken_easyconfigs.py broken.eb myeasyconfigs GCC-4.9.2.eb
    == 2015-03-05 17:02:22,438 fix_broken_easyconfigs.FIX_BROKEN_EASYCONFIGS INFO Processing 3 easyconfigs
    == 2015-03-05 17:02:22,454 fix_broken_easyconfigs.FIX_BROKEN_EASYCONFIGS INFO Backed up broken.eb to broken.eb.bk
    == 2015-03-05 17:02:22,454 fix_broken_easyconfigs.FIX_BROKEN_EASYCONFIGS INFO broken.eb: fixed
    == 2015-03-05 17:02:22,458 fix_broken_easyconfigs.FIX_BROKEN_EASYCONFIGS INFO /home/example/myeasyconfigs/HPL-2.1-intel-2015a.eb: nothing to fix
    == 2015-03-05 17:02:22,461 fix_broken_easyconfigs.FIX_BROKEN_EASYCONFIGS INFO GCC-4.9.2.eb: nothing to fix

This results in ``broken.eb`` being rewritten/fixed, after creating a backup copy ``broken.eb.bk``::

    $ diff -u broken.eb.bk broken.eb
    --- broken.eb.bk	2015-02-25 14:45:52.000000000 +0100
    +++ broken.eb	2015-03-05 16:51:44.000000000 +0100
    @@ -1,4 +1,6 @@
     # licenseheader
    +easyblock = 'ConfigureMake'
    +
     name = 'foo'
     version = '1.2.3'
     
    @@ -7,7 +9,7 @@
     
     toolchain = {'name': 'bar', 'version': '3.2.1'}
      
    -premakeopts = 'FOO=libfoo.%s' % shared_lib_ext
    -makeopts = 'CC=gcc'
    +prebuildopts = 'FOO=libfoo.%s' % SHLIB_EXT
    +buildopts = 'CC=gcc'
     
    -license = 'foo.lic'
    +license_file = 'foo.lic'

.. _install_EasyBuild_develop_script:

``install-EasyBuild-develop.sh``
--------------------------------

*download from:* https://github.com/easybuilders/easybuild-framework/blob/main/easybuild/scripts/install-EasyBuild-develop.sh

A script to set up an EasyBuild development environment.
For more information, see :ref:`install_latest_develop_using_script`.

.. _clean_gists_script:

``clean_gists.py``
--------------------------------

*download from:* https://github.com/easybuilders/easybuild-framework/blob/main/easybuild/scripts/clean_gists.py

When using ``--from-pr`` in combination with ``-upload-test-report`` (see
https://github.com/easybuilders/easybuild/wiki/Review-process-for-contributions#automated-testing-of-easyconfigs-pull-requests),
you can end up with a bunch of gists in your GitHub account containing test reports, that may no longer be relevant.

To help with that the ``clean_gists.py`` script is available, to clean up gists containing test reports:

* ``clean_gists.py -p``: delete all gists from closed pull requests (default action if no other action is specified)
* ``clean_gists.py -a``: delete all gists generated by Easybuild
* ``clean_gists.py -o``: delete all gists without a matching pull request (created by using ``-upload-test-report`` without ``--from-pr``)

By default, the script will use the same GitHub account that Easybuild uses (see ``--github-user``); to specify a different GitHub account, use ``-g``.

The script expects that a valid GitHub token for the used GitHub account username is available, see :ref:`github_token`.

```
