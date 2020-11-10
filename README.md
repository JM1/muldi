[//]: # (kate: space-indent on; indent-width 2; word-wrap-column 100; word-wrap on;)
[//]: # (kate: remove-trailing-spaces modified;)

# `muldi` - multi distribution package generator

[![License: GPL v3](
https://img.shields.io/badge/License-GPLv3-blue.svg)](
https://www.gnu.org/licenses/gpl-3.0)

<img src="https://www.svgrepo.com/show/217080/dump-truck-truck.svg" width="48" />

A Python program that helps developers to generate package control files for easy and fine-grained
distribution of reuseable, atomic source code artifacts, e.g. the header and source file of a C++
class, the header of a C++ template class or the source file of a Python module or class. It
supports various distribution formats, e.g. source-based systems such as CMake projects and Gentoo
Portage packages or binary package distribution systems such as Debian packages.

:warning: **NOTE**: This is just a sunday-morning idea and has not yet been implemented!

Feel free to [contact me](mailto:jakobmeng@web.de), if you want to see `muldi` realized.

## Problem statement

You develop code in one project. Then you work on another project and you realize, that you want to
reuse parts from the first project. You do not want to simply copy the files because then you have
to maintain the same code twice. You know how to refactor your code to increase its reuseability
and you know plenty of ways to distribute it as an individual code project, either as source code
or as binary packages. Unfortunately, writing and maintaining `CMakeLists.txt` or `*.emerge`
or package `control` files is a tedious and repetitive task. In practice, you trade source
code maintenance costs off agaist increased package maintenance and distribution costs. The more
fined-grained your project landscape gets, the more you spend on writing project management code.

## Assumption

Fine-grained or atomic code artifacts with explicit dependency management improve code reuse.
The smaller they are, the better their reuse.

## Approach

Why not generate redistributable software packages for each code artifact automatically?

The observation is that source files written in the same programming language, usually share a
common code, project management and directory structure. For example, in C++ projects code is
located in `include` and `src` subfolders, headers install to `/usr/include`, binaries to
`/usr/bin` and libraries to `/usr/lib/x86_64-linux-gnu/`. Each code project has a name and a list of
build or runtime dependencies. Dependencies vary in quantity, names and versions between different
packaging systems, e.g. `libboost-all-dev` on Debian and `boost` on Arch Linux.

With `muldi`, you declare essential project metadata in a `.muldi.yml`, e.g. the project's name and
its dependencies. `muldi` detects what programming language you have used, what kind of
software you wrote (Found `int main()`? An application! `*.hpp`, but no `*.cpp` found? Header-only
library!) and then generates project management and packaging code for you.

*NOTE:* `muldi` does *NOT* try to support every possible packaging feature, instead it aims for the
opposite: Exploit structural similarities, respect syntactic differences (e.g. names of
dependencies) and then generate package control files such as `CMakeLists.txt` and Debian package
`control` from predefined templates.

# Usage

```sh
TODO
```

`muldi` is best used with a CI/CD system such as [`GitLab CI/CD`](https://docs.gitlab.com/ce/ci/) or
[`Travis CI`](https://travis-ci.com/) to automatically build packages on each commit and upload them
to a package repository, e.g. for [Debian](https://wiki.debian.org/DebianRepository/).

## Related work

* [`CMake`](https://cmake.org/documentation/):
  A developer writes one (or more) `CMakeLists.txt` file per code project. End users then run
  `cmake` to generate e.g. native `Makefile`'s to build the whole code on their platform using their
  own toolset.
  `muldi` tries to reduce the maintenance burden for developers: It generates meta build files such
  as `CMakeLists.txt` or package-build files such as `control` files for Debian packages. This can
  also be beneficial for end users because it makes it easier for developers to generate end user
  installable packages with CI/CD pipelines.

## Example

```yaml
# All keys except 'profiles' are optional and will be auto-detected if possible
language: cpp # or: cpp17, python, python2, python3

packages:
- deb
- rpm
- cmake

assembly?/type?/mode?/module?: library
# or: header-library, static-library, dynamic-library, executable

name: lorem-ipsum

description: |
  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut
  labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco
  laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in
  voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat
  non proident, sunt in culpa qui officia deserunt mollit anim id est laborum

profiles:
- debian:
  dependencies:
  - cmake
  - gcc
```

# Principles

* easy to setup
  - one additional file `.muldi.yml`
  - self-explaining examples
  - auto-versioning if possible, e.g. using timestamp and git commit hash
* easy to migrate
  - do not break existing `muldi` configs with new `muldi` releases
  - external `muldi` interface (e.g. `.muldi.yml`) is backwards compatible
* easy to contribute and maintain
  - well-known and established programming language (Python)
  - GitLab/GitHub-based development work flow (Fork & Merge, CI)
  - be happy about extensions, e.g. languages, profiles, ...
  - internals may change at any time
* minimal feature set which is required for automatic code artifact management
  - only common features across source code and package managers are supported,
    e.g. `name`, `dependencies`
  - no optional dependencies (?), e.g.
    [`Recommends` or `Suggests` in Debian](
     https://www.debian.org/doc/debian-policy/ch-relationships.html),
    [`Weak Dependencies` in Fedora](
     https://fedoraproject.org/wiki/Packaging:WeakDependencies) or
    [`Recommends` and `Suggests` in SUSE (>=10)](
     https://en.opensuse.org/openSUSE:Build_Service_cross_distribution_howto).
    Reasons:
    + support and implementation not consistent (?)
    + [`Optional dependencies don’t work`](
       https://michael.stapelberg.ch/posts/2019-05-23-optional-dependencies/)

## Getting Involved

Feel free to [contact me](mailto:jakobmeng@web.de).

## Authors

* **Jakob Meng** - [JM1](https://github.com/JM1)

See also the list of [contributors](https://github.com/JM1/muldi/contributors) who participated in
this project.

## License

This project is licensed under the GPL v3 License -
see the [LICENSE.md](LICENSE.md) file for details.
