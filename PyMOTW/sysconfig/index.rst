===================================================
sysconfig -- Interpreter Compile-time Configuration
===================================================

.. module:: sysconfig
    :synopsis: Interpreter Compile-time Configuration

:Purpose: Access the configuration settings used to build Python.
:Available In: 2.7 and later

In Python 2.7 :mod:`sysconfig` has been extracted from
:mod:`distutils` to become a stand-alone module.  It includes
functions for determining the settings used to compile and install the
current interpreter.

Configuration Variables
=======================

You can access the build-time configuration settings through two
functions.  :func:`get_config_vars` returns a dictionary mapping the
configuration variable names to values.

.. include:: sysconfig_get_config_vars.py
   :literal:
   :start-after: #end_pymotw_header

The level of detail available through the :mod:`sysconfig` API depends
on the platform where your program is running.  On POSIX systems such
as Linux and OS X, the ``Makefile`` used to build the interpreter and
``config.h`` header file generated for the build are parsed and all of
the variables found within are available.  On non-POSIX-compliant
systems such as Windows, the settings are limited to a few paths,
filename extensions, and version details.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_config_vars.py'))
.. }}}
.. {{{end}}}

If you pass variable names to :func:`get_config_vars`, the return
value is a :class:`list` created by appending all of the values for
those variables together.

.. include:: sysconfig_get_config_vars_by_name.py
   :literal:
   :start-after: #end_pymotw_header

This example builds a list of all of the installation base directories
where modules can be found on the current system.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_config_vars_by_name.py'))
.. }}}
.. {{{end}}}

When you only need a single configuration value, use
:func:`get_config_var` to retrieve it.

.. include:: sysconfig_get_config_var.py
   :literal:
   :start-after: #end_pymotw_header

If the variable is not found, :func:`get_config_var` returns ``None``
instead of raising an exception.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_config_var.py'))
.. }}}
.. {{{end}}}


Installation Paths
==================

:mod:`sysconfig` is primarily meant to be used by installation and
packaging tools.  As a result, while it provides access to general
configuration settings such as the interpreter version, it is
primarily focused on the information needed to locate parts of the
Python distribution currently installed on a system.  The locations
used by for installing a package depend on the *scheme* used.  

A scheme is a set of platform-specific default directories organized
based on the platform's packaging standards and guidelines.  There are
different schemes for installing into a site-wide location or a
private directory owned by the user.  The full set of schemes can be
accessed with :func:`get_scheme_names`.

.. include:: sysconfig_get_scheme_names.py
   :literal:
   :start-after: #end_pymotw_header

There is no concept of a "current scheme" per se.  The default scheme
depends on the platform, and the actual scheme used depends on options
given to the installation program.  If the current system is running a
POSIX-compliant operating system, the default is ``posix_prefix``.
Otherwise the default is ``os.name``.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_scheme_names.py'))
.. }}}
.. {{{end}}}

Each scheme defines a set of paths used for installing packages.  For
a list of the path names, use :func:`get_path_names`.

.. include:: sysconfig_get_path_names.py
   :literal:
   :start-after: #end_pymotw_header

Some of the paths may be the same for a given scheme, but installers
should not make any assumptions about what the actual paths are.  Each
name has a particular semantic meaning, so the correct name should be
used to find the path for a given file during installation.

===========  ===========
Name         Description
===========  ===========
stdlib       Standard Python library files, not platform-specific
platstdlib   Standard Python library files, platform-specific
platlib      Site-specific, platform-specific files
purelib      Site-specific, non-platform-specific files
include      Header files, not platform-specific 
platinclude  Header files, platform-specific
scripts      Executable script files
data         Data files
===========  ===========

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_path_names.py'))
.. }}}
.. {{{end}}}

Use :func:`get_paths` to retrieve the actual directories associated
with a scheme.

.. include:: sysconfig_get_paths.py
   :literal:
   :start-after: #end_pymotw_header

This example shows the difference between the system-wide paths uses
for ``posix_prefix`` and the user-specific values for ``posix_user``.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_paths.py'))
.. }}}
.. {{{end}}}

For an individual path, call :func:`get_path`.

.. include:: sysconfig_get_path.py
   :literal:
   :start-after: #end_pymotw_header

Using :func:`get_path` is equivalent to saving the value of
:func:`get_paths` and looking up the individual key in the dictionary.
If you need several paths, :func:`get_paths` is more efficient because
it does not recompute all of the paths each time.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_path.py'))
.. }}}
.. {{{end}}}

Python Version and Platform
===========================

While :mod:`sys` includes some basic platform identification (see
:ref:`sys-build-time-info`), it is not specific enough to be used for
installing binary packages because ``sys.platform`` does not always
include information about hardware architecture, bit-ness, or other
values that effect the compatibility of binary libraries.  For a more
precise platform specifier, use :func:`get_platform`.

.. include:: sysconfig_get_platform.py
   :literal:
   :start-after: #end_pymotw_header

Although this sample output was prepared on an OS X 10.6 system, the
interpreter is compiled for 10.5 compatibility, so that is the version
number included in the platform string.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_platform.py'))
.. }}}
.. {{{end}}}

As a convenience, the interpreter version from ``sys.version_info`` is
also available through :func:`get_python_version` in :mod:`sysconfig`.

.. include:: sysconfig_get_python_version.py
   :literal:
   :start-after: #end_pymotw_header

:func:`get_python_version` returns a string suitable for use when
building a version-specific path.

.. {{{cog
.. cog.out(run_script(cog.inFile, 'sysconfig_get_python_version.py'))
.. }}}
.. {{{end}}}

.. seealso::

    `sysconfig <http://docs.python.org/library/sysconfig.html>`_
        The standard library documentation for this module.

    :mod:`distutils`
        :mod:`sysconfig` used to be part of the :mod:`distutils` package.

    `distutils2 <http://hg.python.org/distutils2/>`_
        Updates to :mod:`distutils`, managed by Tarek Ziadé.

    :mod:`site`
        The :mod:`site` module describes the paths searched when
        importing in more detail.

    :mod:`os`
        Includes ``os.name``, the name of the current operating system.

    :mod:`sys`
        Includes other build-time information such as the platform.
