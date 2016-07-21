Boost.Build Software Development Systems (SDS) CrossCode C/C++ Compiler
=======================================================================

Overview
--------

This toolset supports the Software Development Systems (SDS) CrossCode
C/C++ Compiler.  The CrossCode C/C++ Compiler is a cross-compiler for
the M68000 family of microprocessors.  Note that this compilers
compile for bare-metal systems.

The toolsets support the following SDS compiler.

* SDS CrossCode 7.4

Supported Development Platforms
```````````````````````````````

The compiler runs on Windows and every effort has been made to ensure
that the toolsets support proper operation on all those platforms.  On
Windows, Cygwin is also fully supported.

The tools should be installed on a per-user basis to allow the
auto-linking to work and to give the user more control over the
versions they have installed.  The SDS CrossCode tool installers allow
for this.

Since each installer has a different default and format, so the
installation directory must be specified explicitly.  For example, the
compiler version for each family would be installed as follow.

* SDS CrossCode 7.4 - ``${HOME}/opt/sds/sds-crosscode-7.4``

Processor Families
------------------

M68000
``````

The SDS CrossCode C/C++ Compiler is a cross-compiler for the M68000
family of processors that provides C89 and C++-98 compilers.  The
toolset extends the architecture and instruction-set features to
include the M68000 family of processors.

Usage
-----

Once the toolset is configured it should work like any other
Boost.Build toolset within the constraints of the SDS CrossCode C/C++
Compiler and processor family.

A ``using`` directive without parameters searches for the code
generation tools in the normal places.  The first to match wins.

::

   using crosscode ;

Specifying the version performs the same search as above but stops
with the first toolset found that provides that version number.

::

   using crosscode : 7.4 ;

Specifying the path will use the path specified.  If the version does
not match the desired version, it is an error.

::

   using crosscode : 7.4 : /opt/sds/sds-crosscode-7.4/cmd/cc68000 ;

Features
--------

Platform Configuration
``````````````````````

The ``crosscode`` toolset provides a mechanism to support
platform-specific configuration using a linker specification file.

::

   # A jamfile
   exe hello
      : # sources
         hello.cpp

      : # requirements

         <linkflags>"-F hello.spc"
      ;

This can be used to build a program for different 'platforms' using
standard Boost.Build mechanisms.  The example below assumes that two
linker command files, ``platform-a.spc`` and ``platform-b.spc``,
exist.

::

   # A jamfile
   import feature ;

   # define two platforms
   feature.feature platform
      :
         platform-a platform-b
      :
         propagated
         optional
         symmetric
      ;

   exe hello
      : # sources
         hello.cpp

         platform-configuration
      ;

   # generate platform-configuration for each platform
   for p in platform-a platform-b
   {
      alias platform-configuration
	 : # sources
	    $(p).cmd
	 : # requirements
	    <platform>$(p)

            <linkflags>"-F $(p).spc"
	 ;
   }

Issues
------

Run-time system
```````````````

There is still some work to be done selecting the run-time system.
There is dependency on exception-handling, endianess on processors
that have hardware switches, instruction-set, etc.  Also, some systems
come with the source code and a build tool to tailor the run-time
system for a particular system.

Dynamic linking
```````````````

Figure out if there is a way to talk about "dynamic linking" on such a
system.  Certainly, there are relocatable modules, but these aren't
the typical usage.

Platform specification
``````````````````````

First, when cross-compiling for a bare system, the linker controls the
layout of the system in memory.  Typically, this depends heavily on
the details of the system linking for.  This includes, but is not
limited to the following:

- the memory layout of the system (location, size, read/write)
- the locations of various parts of the system
- options for initializing memory
- lots more

This is typically specified to the linker via a linker command file
which is normally given to the linker just like a library would be and
is dependent on the "platform" or "board" or "system" and can change
without any of the other source code of the system changing.

Typically, there is a linker specification that makes sense even if
there is no board specified, though it may be either severely limited
or run only on a simulator.  For example, many embedded processors
have internal RAM and ROM no matter what board they are on.  This is a
nice default so that simple small programs will just link properly and
run.  This is really nice for test programs.

This probably means there is another feature (called "board" for lack
of a better term, I like platform better, but that may conflict with
the way people think about Unix/Linux/Mac OS X/Windows).

Fortunately, with Boost.Build, this can be dealt with by associating
some board-specific source code, libraries, etc. with a board and
select boards to build for at build time.

Multi-threading
```````````````

Note that on a bare-metal system, there is no multi-threading
available.  However, there may be with real-time operating systems
that run on these processors.  Should this be supported in the
compiler or in the operating system file?  Right now, Boost.Build
deals with that in the compiler definitions for ``gcc`` for example
assuming that the ``host-os`` is the ``target-os``.
