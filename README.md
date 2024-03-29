# MUSer2: a grown-up MUS extractor

## Version with compile fixes for Mac

This repository contains the source of [MUSer2](https://bitbucket.org/anton_belov/muser2/src/master/) (cloned 2021-12-19, last bitbucket commit 2017-12-26), with several [changes](https://github.com/DominikPeters/muser2-mac/commit/092f550d59b9357f85862ef4bd64dcd5c888de6c) I had to make to get MUSer2 to compile on MacOS, including on M1 Macs. 

After updating [makefile-common-defs](src/mkcfg/makefile-common-defs#L13) (see below), build using:
```shell
brew install boost gcc
mkdir src/extutil/
ln -s /opt/homebrew/Cellar/boost/1.80.0/include src/extutil/include
# if that's the wrong path, find yours using: brew list boost
cd src/tools/muser2
make
```

The changed files are:

- [Makefile](src/tools/muser2/Makefile)
- [makefile-common-defs](src/mkcfg/makefile-common-defs#L13)
    - In line 13 of this file, in the definition of `CXX`, the reference to homebrew's
      copy of `libstdc++.6.dylib` may need to be updated for your system. You can find
      the right path using `brew list gcc | grep 'libstdc++.6.dylib'`.
- For each solver, a small change to the `SolverTypes.h` file (5 times, [see commit](https://github.com/DominikPeters/muser2-mac/commit/092f550d59b9357f85862ef4bd64dcd5c888de6c)).


## Original README file follows

Born as an MUS extractor, MUSer2 is now a MUS/group-MUS/MES/group-MES/VMUS/VGMUS
extraction framework that implements many different algorithms for the 
computation of the above, and is designed for (relative) ease of trying out and 
experimenting with new ideas. 

The list of references at the end of this README provides pointers to the 
descriptions of various algorithms and techniques used in MUSer2. Please cite 
[1] if you use MUSer2 in your research work.

This source code release is under the GNU General Public License Version 3. See 
COPYING for more details. In essence you are only allowed to distribute a 
modified version of MUSer2 (resp. a program that uses MUSer2) if you make the 
sources of your modifications (resp. the program that uses MUSer2) under GPL as 
well. If you need another license please contact us.

MUSer2 is (c) 2010-2014 Anton Belov and Joao Marques-Silva.  

Please contact Anton Belov (anton@belov-mcdowell.com) in case of bugs or 
questions.

MUSer2 can use a number of different SAT solvers as a back-end. This source 
release comes with wrappers and the source code of the following SAT solvers:
(1) Glucose 3.0 by Gilles Audemard and Laurent Simon
    http://www.labri.fr/perso/lsimon/glucose/
(2) Minisat 2.2 by Niklas Een and Niklas Sorensson
    http://http://minisat.se/  
(3) Minisat GH: the up-to-date version of Minisat by Niklas Sorensson
    https://github.com/niklasso/minisat
(4) Minisat HMUC: the proof-tracing version of Minisat by Vadim Ryvchin
    http://tx.technion.ac.il/~rvadim/index.html
(5) Minisat ABBR: the abbreviating version of Minisat by Jean-Marie Lagniez
    http://fmv.jku.at/lagniez/
(6) PicoSAT v954 by Armin Biere
    http://fmv.jku.at/picosat/
(7) Lingeling ala by Armin Biere
    http://fmv.jku.at/lingeling/        
(8) UBCSAT 1.2 by Dave Tompkins
    http://ubcsat.dtompkins.com/legal
IMPORTANT: the SAT solvers included in this source release are under various
open-source licences. If you use MUSer2, you are indirectly using at least one
of these SAT solvers and it is YOUR responsibility to ensure that your use 
abides by their licensing terms.
       
********************************************************************************
DEPENDENCIES

(1) Some of the code uses Boost Graph Library, as such you will need a 
    header-only distribution of Boost. In case it is installed in a non-standard 
    location, add a symlink to boost include directory to src/extutil/include

(2) As some of the code is written in C++11 gcc 4.7 or later is required to 
    compile the tool.
    
(3) The experimental multi-threaded code (enabled only when mt=1 is passed to
    make) requires Intel Thread Building Blocks (TBB) 3.0 libraries. Add a 
    symlink called tbb30 to src/extutil directory. 
    WARNING: the multi-threaded code has not been touched for over 2 years and 
    is probably broken and inconsistent with the changes that have been made 
    since then. It is not recommended to use it, and it will probably be removed
    in the future.     

********************************************************************************
COMPILATION

This source code release is a snapshot of a subset of a large research framework
called BOLT, developed by J. Marques-Silva and collaborators. As such the 
directory structure and the compilation process is more involved than required 
for MUSer2. Its also a bit fragile, and will, hopefully be fixed.

To compile:

cd ./src/tools/muser2
make

To clean up all directories:

cd ./src/tools/muser2
make allclean

To rebuild dependencies:

cd into directory you want to rebuild for
make deps

The key file for compilation tweaks is ./src/mkcfg/makefile-common-defs

Compilation options:

make mkm=prof -- profiling build
make mkm=val  -- optimized, with debug symbols
make mkm=ass  -- optimized, with assertions enabled
make mkm=chk  -- checked build (code inside CHK(x) macros is compiled in)
make mkm=dbg  -- non-optimized debug build (code in DBG(x) macros is compiled in)

make m32=1    -- build a 32-bit executable instead of 64-bit; you might need to
                 add the path to 32-bit libraries to mkcfg/makefile-common-defs
                 (around line 56).          
                 Note: all ints are 32-bit always, so this only affects pointers,
                 and has not been as thoroughly tested as the 64 bit compile.
                 
make xm=xp    -- compile all the experimental code, i.e. anything under
                 #ifdef XPMODE in the source (use at your own risk)

make mt=1     -- include multi-threaded code (requires TBB); do not do it.

make it       -- compile without checking non-local dependencies

NOTE: if you're running into weird problems it might be because there are 
symbols accross the static libraries. By default the linker will take the 
object code of the first one it sees and ignore the rest, and you will never 
know about it. There's a explaining how to deal with this in 
src/mkcfg/makefile-common-defs

This source distribution also includes a source code for a (thin) API that allows
to link in and execute muser2 from other soft. The API is in src/api.
See README in that directory for instructions.


********************************************************************************
USAGE

muser2 -- display all available options and parameters. Please read the output
          carefully. If something is unclear send us an email.

Some examples:

1. computation of a MUS:

muser2 ../examples/bitops0.cnf  

2. computation of a group-MUS:

muser2 -grp ../examples/bitops0.gcnf

3. computation of a variable-MUS:

muser2 -var ../examples/c499_gr_2pin_w5.shuffled.cnf.gz

4. computation of an interesting-VMUS:

muser2 -var -grp ../examples/b21_242.vgcnf.gz

5. computation of an MES using chunking mode and specialized model rotation

muser2 -irr -chunk 1000 -imr ../examples/bw_large.a.cnf


********************************************************************************
AUTHORS

Anton Belov (anton@belov-mcdowell.com) -- MUSer2.
Joao Marques-Silva (joao.marques-silva@ucd.ie) -- BOLT, initial version of MUSer.

Contributors:

Alessandro Previti

Please contact Anton Belov (anton@belov-mcdowell.com) in case of bugs or 
questions.

********************************************************************************
MUSer2 REFERENCES

[1] A. Belov and J. Marques-Silva, "MUSer2 : An Efficient MUS Extractor, System 
    Description", JSAT, 2012. 
    * this is the tool paper for MUSer2.    
[2] A. Belov, I. Lynce, and J. Marques-Silva, "Towards efficient MUS extraction"
    AI Commun., 2012.
    * hybrid algorithm, recursive model rotation
[3] A. Belov, A. Ivrii, A. Matsliah, and J. Marques-silva, "On Efficient 
    Computation of Variable MUSes", SAT-2012, 2012.
    * variable MUSes
[4] A. Belov, M. Janota, I. Lynce, and J. Marques-Silva, "On Computing Minimal 
    Equivalent Subformulas," in CP-12, 2012, pp. 1-17.
    * MESes
[5] J. Marques-Silva, M. Janota, and A. Belov, "Minimal Sets over Monotone 
    Predicates in Boolean Formulae," in CAV-2013, pp. 1-16.
    * progression-based algorithm
[6] A. Belov, M. Jarvisalo, and J. Marques-Silva, "Formula preprocessing in MUS 
    extraction," TACAS 2013, pp. 1-15, 2013.
    * preprocessing
    
