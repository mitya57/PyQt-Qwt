# PyQt-Qwt
Python PyQt wrapper for Qwt6

The code is tested for python2/3 and Qt4/5.
oscilloscope.py is unfinished.
animation.py works only for qt5.
All the other examples have been tested to work on 
Debian Linux.
The qwt include files must be patched to build PyQt-Qwt.



## BUILD:

### ******* IMPORTANT *********

The PyQt-Qwt doesn't build against unpatched Qwt version 6.1.3.
The header files need to be patched with 06_python_compat.patch
but for convenience the patched files are kept in the header
directory.

For Qwt version < 6.1.5 the following is needed before compiling:

$ cp -a /usr/include/qwt header

$ cp header/qwt*.h header/qwt/

### Linux:

Dependencies in Debian:

$ sudo apt-get install pyqt5-dev pyqt5-dev-tools python3-pyqt5 libqwt-qt5-dev libqwt-headers

A proper configure.py file has been added. To use it on Debian
which supports coexisting Qt libraries (4 and 5) you need to 
add QT_SELECT ahead of the command line.

All systems are not exactly equal. python may refer to python3 on some systems.
On Debian systems the Qt5 version of Qwt is named libqwt-qt5.so but the default name is
libqwt.so. Remove the  --qwt-lib=qwt-qt5 if the name extension is not used on your system.


$ QT_SELECT=qt5 python3 configure.py --qwt-incdir=header/qwt --qwt-libdir=/usr/lib --qwt-lib=qwt-qt5

$ QT_SELECT=qt4 python3 configure.py --qwt-incdir=header/qwt --qwt-libdir=/usr/lib --pyqt=PyQt4

$ make

$ sudo make install

$ cd qt5examples

$ python3 bode.py

$ for name in *.py; do python3 $name; done

## Windows:

MSVC2015

Assuming the default installation directory, c:\qwt-6.1.3

Compile Qwt with the following parts in qwtconfig.pri commented out:

#QWT_CONFIG += QwtDll

#QWT_CONFIG += QwtSvg

#QWT_CONFIG += QwtOpenGL

#QWT_CONFIG += QwtMathML


Open "Qt 5.9.2 32-bit for Desktop (MSVC2015)" command prompt
run

"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat"

copy header\qwt*.h c:\qwt-6.1.3\include

python configure.py --qwt-incdir=c:\qwt-6.1.3\include --qwt-libdir=c:\qwt-6.1.3\lib

nmake

### Debugging

It took me a lot of time to find out how to debug with gdb, and there is
a bug/(feature) in gcc that makes the procedure more problematic.
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=61918

I am using gcc 8.1.0

$ QT_SELECT=qt5 python3-dbg configure.py --qwt-incdir=header/qwt --qwt-libdir=/usr/lib --qwt-lib=qwt-qt5 --trace --debug

Then do the following in Qwt/Makefile

Replace "-isystem" with "-I"

Remove "$(shell dpkg-buildflags --get CFLAGS) -D_FORTIFY_SOURCE=2". The line looks very Debianish, don't know if it exists on other systems. Will check.

Then do:

$ make; sudo make install

Then debug with

$ gdb --args python3-dbg
$ run barchart.py

### Usage

The pyuic5 doesn't fully work with PyQt-Qwt.

After creating the python file.

pyuic5 uifile.ui -o ui_uifile.py

you need to edit the file afterwards.

Add 

from PyQt5.Qwt import *

at the top of the file.

Then remove all lines with

import qwt_

from the bottom of the file.

The following command will do this automatically

pyuic5 uifile.ui |grep -v "from qwt_" | sed 's/# WARNING! All changes made in this file will be lost!/from PyQt5.Qwt import */g' > ui_uifile.py

but it needs sed and grep to be installed on your computer.

### Status

2019-02-14
  * Compiles with sip >= 4.18.
  * Compiles with Qwt >= 6.1.2. Tested for version 6.1.2, 6.1.3, 6.1.4
  * All examples except for oscilloscope.py do work.



