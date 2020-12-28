# Project Eberon

Project Eberon is a collection of software packages designed to help students wishing to learn or extend the [Project Oberon 2013](http://www.projectoberon.com) system. The original, if less extensive, _Project Oberon 2013_ web-site is also still available at the [ETH Zurich Project Oberon](https://people.inf.ethz.ch/wirth/ProjectOberon/index.html) web-site. In case you were wondering, Eberon is a anagram of Oberon.

The initial goal of Project Eberon is to bring together useful tools from various other repositories, rationalize their organization, simplify their presentation, and document how to use them effectively together to obtain, build, and deploy the original _Project Oberon 2013_ sources within an emulated RISC5 environment. 

Stretch goals may include tooling for targeting one or more FPGA systems beyond the original Digilent Spartan 3 platform which has since been discontinued. Projects extending the original Project Oberon 2013 system as described by Wirth such as [Project Oberon Extended](https://github.com/andreaspirklbauer/Oberon-extended) or targeting other ISAs such as [RISC-V](https://github.com/solbjorg/oberon-riscv) are not in scope. 

We will (eventually) avoid here using git submodules or linked references to other repositories on the Internet and instead provide an opinionated distillation of the useful features from each in an attempt to create an easier to understand and use byproduct. Links to the original and proper credit will, of course, be provided (and maintained) for all included source code, utilities, and documentation to the extent possible.

The end result, I hope, will be to make learning the complete _Project Oberon 2013_ system as frictionless as possible. I hope to (eventually, alas) add wiki entries to this repository demonstrating how to use these tools to closely follow the original _Project Oberon 2013_ documentation from beginning to end without the need for a capable hardware FPGA platform. 

## Contents

### Sources

* `src/Oberon/`     Project Oberon 2013 unmodified sources.<sup>3</sup>
* `src/Runtime/`    Project Norebo RISC5 emulator and operating system interface.<sup>2</sup>
* `src/Norebo/`     Project Norebo new and modified modules allowing headless operation.<sup>2</sup>

### Utilities

* `bin/dos2oberon`  Perl script to convert a DOS format file to the oberon file format.<sup>1</sup>
* `bin/oberon2dos`  Perl script to convert an Oberon format file to the dos file format.<sup>1</sup>
* `build-norebo.sh` Script to (re)build Norebo.<sup>2</sup>
* `build-image.sh`  Script to build a runnable Project Oberon disk image.<sup>2</sup>

### Other

* `manifest.csv`    Lists the set of files that will define the resulting Oberon disk image. 
* `obj/Bootstrap/`  Pre-compiled modules used to bootstrap Norebo.<sup>2</sup>

---

<sup>1</sup> from https://github.com/andreaspirklbauer/Oberon-building-tools
<sup>2</sup> from https://github.com/pdewacht/project-norebo
<sup>3</sup> from https://www.inf.ethz.ch/personal/wirth/ProjectOberon/Sources/

## Building a Project Oberon 2013 disk image

In addition to the original Project Oberon 2013 sources, this repository also contains a tool to build Oberon filesystem images based on a successful build of the `norebo` utility.  Use it like so:

    ./build-norebo.sh
    ./build-image.sh /src/Oberon

...where `src/Oberon` is replaced with the directory containing the Project Oberon sources you wish to compile. By default, these sources are located in `src/Oberon` per the example given above.

The first script compiles the `norebo` binary which the second script uses to produce a runnable Oberon disk image. This resulting disk image is located at `build/Oberon.dsk` and can be run using using the [Project Oberon RISC emulator](https://github.com/pdewacht/oberon-risc-emu).

## Porject Norebo file handling and other notes

Supporting Oberon modules are stored in `src/Norebo/`: a virtual file system (`VDiskUtil`/`VFile`) and a static linker for the Inner Core. All this is based on code from PO2013.

New files are always created in the current directory. Old files are first looked up in the current directory and if they are not found, they are searched for in the path defined by the `OBERON_PATH`
environment variable. Files found via `OBERON_PATH` are always opened read-only.

Files are not integrated with the garbage collector. If you don't close a file, it will remain open until Norebo exits.

Most runtime errors do not print a diagnostic message. Here's a table of exit codes:

 Exit code | Meaning
----------:|:------------------------------
      1..7 | possibly a Modules error
         5 | (also) unknown command
       101 | array index out of range
       102 | type guard failure
       103 | array or string copy overflow
       104 | access via NIL pointer
       105 | illegal procedure call
       106 | integer division by zero
       107 | assertion violated
