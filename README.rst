Boost.Build Toolset for Software Development Systems (SDS) CrossCode C/C++ Compiler
===================================================================================

.. contents::

Overview
--------

This directory contains a Boost.Build toolset for the Software
Development Systems (SDS) CrossCode C/C++ Compiler and documentation.

Note that this compiler is a cross-compiler for the M68000 family of
processors.

Toolset
-------

See the `documentation <crosscode.rst>`__ for a description of how to
use the SDS CrossCode C/C++ Compiler.

See the implementation files for the toolset for implementation
details.

* `CrossCode <crosscode.jam>`__

Testing
-------

This project can be tested using the `Boost.Build Toolset Tester
<https://github.com/tee3/boost-build-toolset-tester>`__.  There is a
branch ``devel-sds``, which configures the toolsets.

.. code:: sh

   $ b2 --test-config=user-config.jam \
         toolset=crosscode \
         link=static
