# Writing easyconfig files in YAML syntax (.yeb format)

!!! warning
    This page will soon replace <https://docs.easybuild.io/en/latest/Writing_yeb_easyconfig_files.html>.

    **
    It still needs to be ported from *reStructuredText* (.rst) to *MarkDown* (.md),  
    and you can help with that!
    **

    - source: [`docs/Writing_yeb_easyconfig_files.rst` in `easybuilders/easybuild` repo](https://raw.githubusercontent.com/easybuilders/easybuild/develop/docs/Writing_yeb_easyconfig_files.rst)
    - target: [`docs/writing-yeb-easyconfig-files.md` in `easybuilders/easybuild-docs` repo](https://github.com/easybuilders/easybuild-docs/tree/main/docs/writing-yeb-easyconfig-files.md)

    See <https://github.com/easybuilders/easybuild-docs> for more information.

```rst
.. _easyconfig_yeb_format:

Writing easyconfig files in YAML syntax (``.yeb`` format) **[IN DEVELOPMENT]**
==============================================================================

.. note::
    Because support for easyconfig files in YAML syntax (a.k.a. ``.yeb`` files) is still *in development*,
    using them currently requires enabling the use of experimental features (``--experimental``),
    see also :ref:`experimental_features` .

    An up-to-date overview of current progress on support for ``.yeb`` easyconfigs is available at
    https://github.com/easybuilders/easybuild-framework/issues/1407.

Useful links:

* YAML syntax specification: http://www.yaml.org/spec/1.2/spec.html

.. contents::
    :depth: 3
    :backlinks: none

.. _easyconfig_yeb_format_requirements:

Requirements
------------

To use ``.yeb`` easyconfigs, you need to have:

* an EasyBuild (development) version which is aware of the ``.yeb`` format (i.e., version 2.3.0dev or higher)
* `PyYAML <https://pypi.python.org/pypi/PyYAML>`_ installed and available in your Python search path
  (via ``$PYTHONPATH`` for example), such that ``import yaml`` works

.. _easyconfig_yeb_format_syntax:

Syntax
------

.. _easyconfig_yeb_format_syntax_YAML_header:

YAML header (optional)
~~~~~~~~~~~~~~~~~~~~~~

Easyconfig files in YAML syntax can start with a standard YAML header.

It consists of two lines:

* a line with a '``%YAML``' *directive* which also indicates the YAML version being used
  (the latest YAML version is 1.2, and dates from 2009);
* followed by a *document marker* line '``---``' (which is used to separate directives from content)

For example::

    %YAML 1.2
    ---

This header is optional, but we recommend including it; one advantage is that editor will use proper syntax
highlighting for YAML when the ``%YAML`` directory is included.

.. _easyconfig_yeb_format_syntax_comments:

Comments
~~~~~~~~

Comments can be included anywhere, and are prefixed with a hash character ``#``::

    # this is a comment


.. _easyconfig_yeb_format_syntax_internal_variables:

Internal variables
~~~~~~~~~~~~~~~~~~

To define and use temporary/internal variables in easyconfig files, which can be useful to avoid hardcoding (partial)
easyconfig parameter values, the YAML anchor/alias functionality can be used
(see also http://www.yaml.org/spec/1.2/spec.html#id2765878).

A value can be marked for future reference via an *anchor*, using the ampersand character '``&``'.
Referring to it later is done using an asterisk character '``*``'.

Typically, internal variables are defined at the top of the ``.yeb`` easyconfig file using a list named
``_internal_variables_``, but this is just a matter of style; anchors can be defined throughout the entire file if
desired.

For example, referring to the Python version being used in both the ``versionsuffix`` and list of dependencies can
be done as follows::

    _internal_variables_:
        - &pyver 2.7.10

    versionsuffix: !join [-Python-, *pyver]
    dependencies:
        - [Python, *pyver]

In this example, the ``!join`` is used to concatenate two string values,
see also :ref:`easyconfig_yeb_format_syntax_string_concatenation`.

A more elaborate example of this is the :ref:`easyconfig_yeb_format_examples_goolf1410` example easyconfig.


.. _easyconfig_yeb_format_syntax_string_concatenation:

Concatenating strings and/or variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The standard YAML format does not support the notion of string concatenation.

Since concatenating string values is a common pattern in easyconfig files, the EasyBuild framework
defines the ``!join`` operator to support this.

For example, defining a ``versionsuffix`` that contains the Python version being used (which may be referred to
elsewhere too) can be done as follows::

    _internal_variables_:
        - &pyver 2.7.10

    versionsuffix: !join [-Python-, *pyver]


.. _easyconfig_yeb_format_syntax_escaping:

Escaping string values with single or double quotes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Strings in YAML can be unquoted. However, when they contain special characters they need to be escaped by either single-
or double-quoting the string.

Special characters that require single quotes are: `:` `{` `}` `[` `]` `,` `&` `*` `#` `?` `|` `-` `<` `>` `=` `!` `%` `@` and `\``.
When using single-quoted strings, any single quote inside the string must be doubled to escape it.

If the string contains control characters such as `\n`, it must be escaped with double quotes. 


.. _easyconfig_yeb_format_syntax_easyconfig_parameters:

Easyconfig parameter values
~~~~~~~~~~~~~~~~~~~~~~~~~~~

To define an easyconfig parameter, simply use ``<key>: <value>`` (i.e., use a colon ``:`` as a separator).

In YAML terminology, an easyconfig file is expressed as a *mapping*, with easyconfig parameters as keys.

Three types of values (*nodes*) are supported: *scalars* (strings, integers), *sequences* (lists) and *mappings*
(dictionaries).

.. _easyconfig_yeb_format_syntax_scalars:

Scalar values
#############

Using scalar values is straight-forward, no special syntax is required.

For string values, no quotes must be used (in general).
However, quotes are sometimes required to escape characters that have special meaning in YAML (like '``:``').
(Also see: :ref:`easyconfig_yeb_format_syntax_escaping`)
It's worth noting that there's a subtle difference between using single and double quotes, see
`Flow Scalar Styles <http://www.yaml.org/spec/1.2/spec.html#id2786942>`_.

Examples::

    name: gzip
    version: 1.6

    # single quotes are required for string values representing URLs, to escape the ':'
    homepage: 'http://www.gnu.org/software/gzip/'

    parallel: 1

Multiline strings can be expressed using indentation::

    description:
        gzip is a popular data compression program
        as a replacement for compress

.. _easyconfig_yeb_format_syntax_sequences:

Sequences
#########

Sequence values (a.k.a. lists) can be expressed in different ways, depending on their size.

If there are a limited number of (short) entries the value can be expressed on a single line,
using square brackets '``[``' '``]``' and with comma '``,``' as separator.

Example::

    # quotes are required to escape the ':'
    source_urls: ['http://ftpmirror.gnu.org/gzip/', 'ftp://ftp.gnu.org/gnu/gzip/']

Alternatively indentation can be used for scope, with each entry on its own line,
indicated with a dash and a space ``- ``.

Example::

    # no quotes required here, since there's no ambiguity w.r.t. ':'
    source_urls:
        - http://ftpmirror.gnu.org/gzip/
        - http://ftp.gnu.org/gnu/gzip/
        - ftp://ftp.gnu.org/gnu/gzip/

.. _easyconfig_yeb_format_syntax_mappings:

Mappings
########

Mapping values (a.k.a. dictionaries) are expressed using a colon '``:``' and space as key-value separator,
a comma '``,``' to key-value pairs, and curly braces '``{``' '``}``' to mark the start/end.

For example::

    toolchain: {name: intel, version: 2015b}

.. _easyconfig_yeb_format_syntax_nesting:

Nesting
#######

Different types of values can be nested.

For example, sequence values can be used in a mapping::

    sanity_check_paths: {
        files: [bin/gunzip, bin/gzip, bin/uncompress],
        dirs: [],
    }

And sequences of sequences are also supported::

    osdependencies
        - zlib
        - [openssl-devel, libssl-dev, libopenssl-devel]


.. _easyconfig_yeb_format_syntax_template_values_constants:

Templates values and constants
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Template values can be specified as a part of string values, using ``%(template_name)``.

Template constants are injected by the easyconfig ``.yeb`` parser as *node anchors*,
and can be referred to with an *alias node*, i.e. using an asterisk ``*``.

For example::

    source_urls: [*GNU_SOURCE]
    sources: ['%(name)s-%(version)s.tar.gz']  # equivalent with [*SOURCE_TAR_GZ]

See also :ref:`easyconfig_param_templates`.

.. _easyconfig_yeb_format_syntax_dependencies:

Dependencies
~~~~~~~~~~~~

We updated the way dependencies are specified to match with the new toolchain format (:ref:`easyconfig_yeb_format_new`)
The format is a bit more verbose than before, but easier to read. Each dependency is a list entry, indicated by a dash
and space (`- `). Each entry can specify a ``name: version`` key-value pair, and a ``versionsuffix`` and ``toolchain``.
Only the ``name: version`` pair is required.

Dependencies can also be external modules. In this case, the dependency has to be specified with a ``name`` and the marker 
``external_module: True``. The boolean value is not case-sensitive.


A straightforward example::

    dependencies:
        - libreadline: 6.3
        - Tcl: 8.6.4
        - name: fftw/3.3.4.4
          external_module: True

    builddependencies:
        # empty versionsuffix, different toolchain (GCC/4.9.2)
        - CMake: 3.2.2
          toolchain: GCC, 4.9.2

A more complicated example from a toolchain easyconfig, where also the ``!join`` operator
(see :ref:`easyconfig_yeb_format_syntax_string_concatenation`) and internal variables
(see :ref:`easyconfig_yeb_format_syntax_internal_variables`) are used::

    _internal_variables_:
        - &comp_name GCC
        - &comp_version 4.7.2
        - &comp [*comp_name, *comp_version]

        - &blaslib OpenBLAS
        - &blasver 0.2.6
        - &blas !join [*blaslib, -, *blasver]
        - &blas_suff -LAPACK-3.4.2

        - &comp_mpi_tc [gompi, 1.4.10]

    dependencies:
        - *comp_name: *comp_version
        - OpenMPI: 1.6.4
          toolchain: *comp
        - *blaslib: *blasver
          versionsuffix: *blas_suff
          toolchain: *comp_mpi_tc
        - FFTW: 3.3.3
          toolchain: *comp_mpi_tc
        - ScaLAPACK: 2.0.2
          versionsuffix: !join [-, *blas, *blas_suff]
          toolchain: *comp_mpi_tc

For the full version of this easyconfig file, see the example ``.yeb`` easyconfig
:ref:`easyconfig_yeb_format_examples_goolf1410`.

.. _easyconfig_yeb_format_new:

OS dependencies and sanity check paths
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To specify parameters that used to contain tuples such as ``osdependencies`` and ``sanity_check_paths``, simply use
lists (sequences) instead of tuples.

For example::

    # note: this is eb syntax, will not work in .yeb files
    osdependencies = [('openssl-devel', 'libssl-dev', 'libopenssl-devel')]

Becomes::

    osdependencies: [[openssl-devel, libssl-dev, libopenssl-devel]] 

And::

    # note: this is eb syntax, will not work in .yeb files
    sanity_check_paths = {
        'files': ['fileA', ('fileB', 'fileC')],
        'dirs' : ['dirA', 'dirB'],
    }

Becomes::

    sanity_check_paths: {
        files: [fileA, [fileB, fileC]],
        dirs: [dirA, dirB]
    }

Shorthands for common easyconfig parameters
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Toolchain format
################

The easyconfig parameter ``toolchain`` in .eb files is defined as a dictionary ``{'name':'foo', 'version':'bar'}``. In
the .yeb format, this can be done much easier by just using ``name, version``. E.g::

    # note: this is eb syntax, will not work in yeb files
    toolchain = {'name':'intel', 'version':'2015b'}

becomes::

    toolchain: intel, 2015b

.. _easyconfig_yeb_format_examples:

Examples
--------

.. _easyconfig_yeb_format_examples_gzip16_GCC492:

gzip-1.6-GCC-4.9.2.yeb
~~~~~~~~~~~~~~~~~~~~~~

Example easyconfig for gzip v1.6 using the ``GCC/4.9.2`` toolchain.

.. code::

    %YAML 1.2
    ---
    easyblock: ConfigureMake

    name: gzip
    version: 1.6

    homepage: 'http://www.gnu.org/software/gzip/'
    description:
        gzip is a popular data compression program
        as a replacement for compress

    toolchain: GCC, 4.9.2

    # http://ftp.gnu.org/gnu/gzip/gzip-1.6.tar.gz
    source_urls: [*GNU_SOURCE]
    sources: [%(name)s-%(version)s.tar.gz]

    # make sure the gzip, gunzip and compress binaries are available after installation
    sanity_check_paths: {
        files: [bin/gunzip, bin/gzip, bin/uncompress],
        dirs: [],
    }

    moduleclass: tools

.. _easyconfig_yeb_format_examples_goolf1410:

goolf-1.4.10.yeb
~~~~~~~~~~~~~~~~

Easyconfig file in YAML syntax for the goolf v1.4.10 toolchain.

.. code::

    _internal_variables_:
        - &version 1.4.10

        - &comp_name GCC
        - &comp_version 4.7.2
        - &comp [*comp_name, *comp_version]

        - &blaslib OpenBLAS
        - &blasver 0.2.6
        - &blas !join [*blaslib, -, *blasver]
        - &blas_suff -LAPACK-3.4.2

        - &comp_mpi_tc [gompi, *version]


    easyblock: Toolchain

    name: goolf
    version: *version

    homepage: (none)
    description: |
        GNU Compiler Collection (GCC) based compiler toolchain, including
        OpenMPI for MPI support, OpenBLAS (BLAS and LAPACK support), FFTW and ScaLAPACK.

    toolchain: {name: system, version: system}

    # compiler toolchain dependencies
    # we need GCC and OpenMPI as explicit dependencies instead of gompi toolchain
    # because of toolchain preparation functions
        dependencies:
            - *comp_name: *comp_version
            - OpenMPI: 1.6.4
              toolchain: *comp
            - *blaslib: *blasver
              versionsuffix: *blas_suff
              toolchain: *comp_mpi_tc
            - FFTW: 3.3.3
              toolchain: *comp_mpi_tc
            - ScaLAPACK: 2.0.2
              versionsuffix: !join [-, *blas, *blas_suff]
              toolchain: *comp_mpi_tc

    moduleclass: toolchain



Python-2.7.10-intel-2015b.yeb
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code::

    _internal_variables_:
        - &numpyversion 1.9.2
        - &scipyversion 0.15.1

    easyblock: ConfigureMake

    name: Python
    version: 2.7.10

    homepage: http://python.org/
    description: |
        Python is a programming language that lets you work more quickly and integrate your systems
        more effectively.

    toolchain: intel, 2015b
    toolchainopts: {pic: True, opt: True, optarch: True}

    source_urls: ['http://www.python.org/ftp/python/%(version)s/']
    sources: [*SOURCE_TGZ]

    # python needs bzip2 to build the bz2 package
    dependencies: [
        - bzip2: 1.0.6
        - zlib: 1.2.8
        - libreadline: 6.3
        - ncurses: 5.9
        - SQLite: 3.8.10.2
        - Tk: 8.4.6
          versionsuffix: -no-X11
      # - OpenSSL: 1.0.1m
      #   OS dependency should be preferred if the os version is more recent then this version, its
      #   nice to have an up to date openssl for security reasons
    ]

    osdependencies: [[openssl-devel, libssl-dev, libopenssl-devel]]

    # order is important!
    # package versions updated May 28th 2015
    exts_list: [
        [setuptools, '16.0', {
            source_urls: ["https://pypi.python.org/packages/source/s/setuptools/"],
        }],
        [pip, 7.0.1, {
            source_urls: ["https://pypi.python.org/packages/source/p/pip/"],
        }],
        [nose, 1.3.6, {
            source_urls: ["https://pypi.python.org/packages/source/n/nose/"],
        }],
        [numpy, *numpyversion, {
            source_urls: [
                [!join ["http://sourceforge.net/projects/numpy/files/NumPy/", *numpyversion], download]
            ],
            patches: [
                numpy-1.8.0-mkl.patch, # % numpyversion,
            ],
        }],
        [scipy, *scipyversion, {
            source_urls: [
                [!join ["http://sourceforge.net/projects/scipy/files/scipy/", *scipyversion], download]],
        }],
        [blist, 1.3.6, {
            source_urls: ["https://pypi.python.org/packages/source/b/blist/"],
        }],
        [mpi4py, 1.3.1, {
            source_urls: ["http://bitbucket.org/mpi4py/mpi4py/downloads/"],
        }],
        [paycheck, 1.0.2, {
            source_urls: ["https://pypi.python.org/packages/source/p/paycheck/"],
        }],
        [argparse, 1.3.0, {
            source_urls: ["https://pypi.python.org/packages/source/a/argparse/"],
        }],
        [pbr, 1.0.1, {
            source_urls: ["https://pypi.python.org/packages/source/p/pbr/"],
        }],
        [lockfile, 0.10.2, {
            source_urls: ["https://pypi.python.org/packages/source/l/lockfile/"],
        }],
        [Cython, '0.22', {
            source_urls: ["http://www.cython.org/release/"],
        }],
        [six, 1.9.0, {
            source_urls: ["https://pypi.python.org/packages/source/s/six/"],
        }],
        [dateutil, 2.4.2, {
            source_tmpl: python-%(name)s-%(version)s.tar.gz,
            source_urls: ["https://pypi.python.org/packages/source/p/python-dateutil/"],
        }],
        [deap, 1.0.2, {
            # escaped with quotes because yaml values can't start with %
            source_tmpl: "%(name)s-%(version)s.post2.tar.gz",
            source_urls: ["https://pypi.python.org/packages/source/d/deap/"],
        }],
        [decorator, 3.4.2, {
            source_urls: ["https://pypi.python.org/packages/source/d/decorator/"],
        }],
        [arff, 2.0.2, {
            source_tmpl: liac-%(name)s-%(version)s.zip,
            source_urls: ["https://pypi.python.org/packages/source/l/liac-arff/"],
        }],
        [pycrypto, 2.6.1, {
            modulename: Crypto,
            source_urls: ["http://ftp.dlitz.net/pub/dlitz/crypto/pycrypto/"],
        }],
        [ecdsa, '0.13', {
            source_urls: ["https://pypi.python.org/packages/source/e/ecdsa/"],
        }],
        [paramiko, 1.15.2, {
            source_urls: ["https://pypi.python.org/packages/source/p/paramiko/"],
        }],
        [pyparsing, 2.0.3, {
            source_urls: ["https://pypi.python.org/packages/source/p/pyparsing/"],
        }],
        [netifaces, 0.10.4, {
            source_urls: ["https://pypi.python.org/packages/source/n/netifaces"],
        }],
        [netaddr, 0.7.14, {
            source_urls: ["https://pypi.python.org/packages/source/n/netaddr"],
        }],
        [mock, 1.0.1, {
            source_urls: ["https://pypi.python.org/packages/source/m/mock"],
        }],
        [pytz, '2015.4', {
            source_urls: ["https://pypi.python.org/packages/source/p/pytz"],
        }],
        [pandas, 0.16.1, {
            source_urls: ["https://pypi.python.org/packages/source/p/pandas"],
        }],
    ]

    moduleclass: lang

```
