.. _man-packages:

**********
 Packages
**********

Julia has a built-in package manager for installing add-on functionality written in Julia.
It can also install external libraries using your operating system's standard system for doing so, or by compiling from source.
The list of registered Julia packages can be found at :ref:`available-packages`.
All package manager commands are found in the ``Pkg`` module, included in Julia's Base install.

Package Status
--------------

The ``Pkg.status()`` function prints out a summary of the state of packages you have explicitly required, and those that are present because they are dependencies of one or more of those required packages.
Initially, you'll have no packages installed::

    julia> Pkg.status()
    INFO: Initializing package repository /Users/$USER/.julia
    INFO: Cloning METADATA from git://github.com/JuliaLang/METADATA.jl
    No packages installed.

Your package directory is only initialized the first time you run a ``Pkg`` command that expects it to exist.
Here's an example non-trivial set of required and additional packages::

    julia> Pkg.status()
    Required packages:
     - Distributions                 0.2.9
     - UTF16                         0.2.0
    Additional packages:
     - NumericExtensions             0.2.14
     - Stats                         0.2.7

Package can be in various more complicated states, which is indicated by annotations to the right of the installed package version.
We will explain these as we encounter them.
For programmatic usage, ``Pkg.installed()`` returns a dictionary mapping installed package names to the version of that package which is installed::

    julia> Pkg.installed()
    ["Distributions"=>v"0.2.9","Stats"=>v"0.2.7","UTF16"=>v"0.2.0","NumericExtensions"=>v"0.2.14"]

.. _pkg-install:

Adding and Removing Packages
----------------------------

Julia's package manager is a little unusual in that it is declarative rather than imperative.
This means that you tell it what you want and it figures out exactly what needs to be installed to satisfy those requirements.
So rather than installing a package, you add it to the list of requirements and "resolve" what needs to be installed.
You can add a package to the list of requirements with the ``Pkg.add`` function::

    julia> Pkg.status()
    No packages installed.

    julia> Pkg.add("Distributions")
    INFO: Installing Distributions v0.2.9
    INFO: Installing NumericExtensions v0.2.14
    INFO: Installing Stats v0.2.7
    INFO: REQUIRE updated.

    julia> Pkg.status()
    Required packages:
     - Distributions                 0.2.9
    Additional packages:
     - NumericExtensions             0.2.14
     - Stats                         0.2.7

What this actually does is add ``Distributions`` to your ``~/.julia/REQUIRE`` file::

    $ cat ~/.julia/REQUIRE
    Distributions

You can edit the ``~/.julia/REQUIRE`` file by hand and then run ``Pkg.resolve()`` to satisfy the new requirements::

    $ echo UTF16 >> ~/.julia/REQUIRE

    julia> Pkg.resolve()
    INFO: Installing UTF16 v0.2.0

    julia> Pkg.status()
    Required packages:
     - Distributions                 0.2.9
     - UTF16                         0.2.0
    Additional packages:
     - NumericExtensions             0.2.14
     - Stats                         0.2.7

Modifying ``~/.julia/REQUIRE`` by hand and calling ``Pkg.resolve()`` is equivalent to calling ``Pkg.add()``.
The full format of the ``REQUIRE`` file is described below.

When you decide that you don't want to have a package around any more, you can use ``Pkg.rm`` to remove the requirement for it from the ``REQUIRE`` file::

    julia> Pkg.rm("Distributions")
    INFO: Removing Distributions v0.2.9
    INFO: Removing Stats v0.2.7
    INFO: Removing NumericExtensions v0.2.14
    INFO: REQUIRE updated.

    julia> Pkg.status()
    Required packages:
     - UTF16                         0.2.0

     julia> Pkg.rm("UTF16")
    INFO: Removing UTF16 v0.2.0
    INFO: REQUIRE updated.

    julia> Pkg.status()
    No packages installed.

Again, this is equivalent to editing the ``REQUIRE`` file to remove the named package and then running ``Pkg.resolve()`` to update the set of installed packages to match.
