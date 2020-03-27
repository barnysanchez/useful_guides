# Python Virtualization

Disclaimer:  A lot of the steps and information in this document were not my own. In some cases I copied verbatim and redacted sections to adjust to my overall intention. 

To know...

**Multiple Python versions:** Different installations of Python on the same machine, 2.7 and 3.4 for example.

**Virtual environments:** isolated independent environments that can have both a specific version of Python and of any project-specific packages installed within them, without affecting any other projects.

A virtual environment (also called a venv) is a Python environment such that the Python interpreter, libraries and scripts installed into it are isolated from those installed in other virtual environments, and (by default) any libraries installed in a “system” Python, i.e. one which is installed as part of your operating system.


## A) The motivation

There were several motivations to look at Python development virtualization options.

- Motivation 1: Testing of Python features that are version specific. For example, I read that Python version 3.8.2 introduced [positional only parameters](https://www.python.org/dev/peps/pep-0570/) but I wasn't ready to simply install 3.8.2 as my default Python version in my Mac, as I could run into the potential of unsupported libraries and code I developed in previous versions not running all of the sudden. So installing multiple versions of Python in your system and switching back and forth between versions is a great use case here.

- Motivation 2: Multiple Python projects coded in the same version, but using different version libraries. To be specific, let's say that one is using Python 3.5 but there is the need of using and testing different versions of Django. So the concept here would be to have different "encapsulations" (or versions) of a project each with the desired Django version installed. Why would you want to do that? you may want to test backward/forward compatibility, or new features being introduced in the new Django version but want to make sure all previous functionality is still working.  You can effectively then make 2 copies of the project in different directories and operate different versions of Django using the same version of Python. However, think about it, what if you want to test different Python versions with different Django versions? the same concepts/solution should apply. 

- Motivation 3: Complete project isolation. It's similar to the previous, except that you just want to completely isolate one project from another. Maybe you are just starting a project from scratch, this is the time to think about that isolation since inception. At any given time in the project development you will figure out that a certain library is required, or file, you know exactly what is what for that project, versus another completely unrelated project. What's the alternative to this? that many people will just dump all of their files in one directory, and over time as they code different projects, they loose track of what is what. So order and discipline over time goes a long way.

- Motivation 4: Packaging of a program. Let's say that you worked on this great project and now peers or friends want you to share it with them. However you are inmediately bombarded with questions because you shared your script files, they attempted to run them (> python script.py) and it just doesnt' work, because they don't have the same Python version you have or because they are missing a lot of libraries and things that you used in your code, and they would have to install and configure different things to make it run. So rather than giving them the raw *.py files, you can just package it for them. Packaging approaches can be using frozen binaries, or even Docker containers. Again, think about it, if they are not versed with Docker and containers, then you run into the exact same problems and expose yourself to being bombarded with support questions. You ain't got time for that!

## B) The options and what they provide

Unravel your Google search expertise and quickly will find several solutions, and just as quickly you will become confused of what is what and when to use it. 

### Option 1: pyenv

A Python version manager. Installs different versions and flavors of Python interpreters.This is a solution that allows you install multiple versions of Python and switch between one and the other. You can set a global version of Python with Pyenv, or you can specify a Python version on a per directory basis if you want. 

### Option 2: pyenv-virtualenv

The pyenv-virtualenv is a pyenv plugin that helps you manage virtual environments and dependencies. So for the motivation 2 above, the case where for example you would have 1 Python version but would want different library versions across projects, then this is the solution to use in addition to Pyenv.

### Option 3: venv

A tool to create isolated virtual environments from a Python interpreter. If you are using a single Python version and need to just isolate projects in different virtual environments, then this is the way to go. You can however mix venv and pyenv to have isolated projects operating in different versions.

### Option 4: pyvenv

Notice that it may get you confused with Option's 2 pyenv-virtualenv but they are different. Basically pyvenv was the tool recommended at some point but now deprecated.

> From Pydocs: https://docs.python.org/3/library/venv.html

```
Deprecated since version 3.6: pyvenv was the recommended tool for creating virtual environments for Python 3.3 and 3.4, and is deprecated in Python 3.6.

Changed in version 3.5: The use of venv is now recommended for creating virtual environments.
```


## C) Details of the previous options

### Option 1 details: pyenv

https://medium.com/python-every-day/python-development-on-macos-with-pyenv-2509c694a808

### Option 2 details: pyenv-virtualenv

https://medium.com/python-every-day/python-development-on-macos-with-pyenv-virtualenv-ec583b92934c

### Option 3 details: venv

Not exactly specific to venv only, but it covers the basics:

https://www.freecodecamp.org/news/manage-multiple-python-versions-and-virtual-environments-venv-pyenv-pyvenv-a29fb00c296f/

------------

## D) PUTTING Option 1 and Option 3 TOGETHER INTO PRACTICE

### Initial Setup:

- Initial HW/OS setup: MacOs running Catalina (10.15.3)
- At the time (March 26th, 2020) that particular OS ships by default with Python 2.7.16. This version is deprecated. 

### What was the desired project:

1) Simple "hello world" script.
2) Code the previous in Python 3.7.6.
3) Create a 1-single file executable/distrutable file of the previous using *pyinstaller*.

Why that specific version of Python?  because at the time of this writing *[pyinstaller](https://www.pyinstaller.org/)* supported Python versions Python 2.7 and 3.5—3.7, and *pyenv* as we will discover in a moment, at the time had Python 3.7.6 as the latest 3.7.x version listed. 

### Steps:

1) Confirm Python version (should read 2.7.16):
```
python --version
```
2) Install pyenv (need to have installed Homebrew already):

```
brew install pyenv and reload profile
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
source ~/.zshrc
```

3) See available Python versions you can install with pyenv. 

```
pyenv install --list
```

4) As of March 26th, 2020 the newest 3.7.x available was 3.7.7. Get ready to install is. As per pyenv documentation all you would need to do is something like:

```
pyenv install 3.7.7
```

***HOWEVER, PAY ATTENTION*** the previous will install things with no problems. If I ran previous command this is what I would get

```
barnysanchez@IBM3R > pyenv install 3.7.7
python-build: use openssl@1.1 from homebrew
python-build: use readline from homebrew
Downloading Python-3.7.7.tar.xz...
-> https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tar.xz
Installing Python-3.7.7...
python-build: use readline from homebrew
python-build: use zlib from xcode sdk
Installed Python-3.7.7 to /Users/barnysanchez/.pyenv/versions/3.7.7
```

***but you want to do this instead...***

```
barnysanchez@IBM3R > env PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.7.7
python-build: use openssl@1.1 from homebrew
python-build: use readline from homebrew
Installing Python-3.7.7...
python-build: use readline from homebrew
python-build: use zlib from xcode sdk
Installed Python-3.7.7 to /Users/barnysanchez/.pyenv/versions/3.7.7

barnysanchez@IBM3R > eval "$(pyenv init -)"
```

***Why did I do those crazy commands?*** because later on when I tried this the first time and used `pyinstaller` later to make my package, I ran into problems, and searching for answers I came across 2 links that explained that I needed to build with CPython shared-library enabled. References:

https://stackoverflow.com/questions/58548730/how-to-use-pyinstaller-with-pipenv-pyenv   (scroll to the bottom, exactly what I followed)

https://github.com/pyenv/pyenv/wiki   (previous link references this one with a lot of good info on it)


5) You can play with a few pyenv commands to see what's been installed so far, etc. Try for example "`pyenv versions`".

6) At this time you have to be very careful of what you do, not because you will brake anything, but because there is a directory hierarchy that you have to watch out to make sure you create projects with the desired Python version on them. 

Before sharing all the parade of commands, let me try to explain in advance what the intention was:

> Step 1: Verify that system Python was still 2.7.16

> Step 2: Create a new directory that would host new project and cd to it

> Step 3: Set the Python 3.7.7 for that working environment (that directory and anything underneath it)

> Step 4: Create the virtual environment at that point, which will "inherit" the Python running in that directory

> Step 5: Update pip package and install pyinstaller

> Step 6: Code our app (in this case just a "hello world")

> Step 7: Create the single-file program with pyinstaller

> Step 8: Test the install file (under /dist directory)

> Step 9: Exit (deactivate) the virtual environment

Now I will map all the previous steps to the actual output when I typed it all in my Mac:

> Step 1: Verify that system Python was still 2.7.16

```
barnysanchez@IBM3R > python --version
Python 2.7.16
```

> Step 2: Create a new directory that would host new project and cd to it

``` 
barnysanchez@IBM3R > pwd
/Users/barnysanchez
 barnysanchez@IBM3R > cd Documents/GitHub
 barnysanchez@IBM3R > ~/Documents/GitHub > mkdir PROJECT2
 barnysanchez@IBM3R > ~/Documents/GitHub > cd PROJECT2
```

> Step 3: Set the Python 3.7.7 for that working environment (that directory and anything underneath it)

```
barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2 > pyenv local 3.7.7
 barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2 > python --version
Python 3.7.7
```

> Step 4: Create the virtual environment at that point, which will "inherit" the Python running in that directory

```
barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2 > python -m venv app2
```

> Step 5: Update pip package and install pyinstaller

```
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2 > pip install --upgrade pip
Collecting pip
  Using cached https://files.pythonhosted.org/packages/54/0c/d01aa759fdc501a58f431eb594a17495f15b88da142ce14b5845662c13f3/pip-20.0.2-py2.py3-none-any.whl
Installing collected packages: pip
  Found existing installation: pip 19.2.3
    Uninstalling pip-19.2.3:
      Successfully uninstalled pip-19.2.3
Successfully installed pip-20.0.2
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2 > pip install pyinstaller
Processing /Users/barnysanchez/Library/Caches/pip/wheels/62/fe/62/4c0f196d1e0dd689e097449bc81d7d585a7de7dd86b081b80b/PyInstaller-3.6-cp37-none-any.whl
Collecting macholib>=1.8
  Using cached macholib-1.14-py2.py3-none-any.whl (37 kB)
Collecting altgraph
  Using cached altgraph-0.17-py2.py3-none-any.whl (21 kB)
Requirement already satisfied: setuptools in ./lib/python3.7/site-packages (from pyinstaller) (41.2.0)
Installing collected packages: altgraph, macholib, pyinstaller
Successfully installed altgraph-0.17 macholib-1.14 pyinstaller-3.6
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2 >
```

> Step 6: Code our app (in this case just a "hello world")

```
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2 > echo "print('Hello world :) !!!')" > app2.py
```

> Step 7: Create the single-file program with pyinstaller

```
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2 > pyinstaller --onefile app2.py

99 INFO: PyInstaller: 3.6
99 INFO: Python: 3.7.7
109 INFO: Platform: Darwin-19.3.0-x86_64-i386-64bit
109 INFO: wrote /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/app2.spec
112 INFO: UPX is not available.
113 INFO: Extending PYTHONPATH with paths
['/Users/barnysanchez/Documents/GitHub/PROJECT2/app2',
 '/Users/barnysanchez/Documents/GitHub/PROJECT2/app2']
113 INFO: checking Analysis
113 INFO: Building Analysis because Analysis-00.toc is non existent
113 INFO: Initializing module dependency graph...
116 INFO: Caching module graph hooks...
123 INFO: Analyzing base_library.zip ...
1645 INFO: Processing pre-find module path hook   distutils
1646 INFO: distutils: retargeting to non-venv dir '/Users/barnysanchez/.pyenv/versions/3.7.7/lib/python3.7'
2575 INFO: Caching module dependency graph...
2666 INFO: running Analysis Analysis-00.toc
2674 INFO: Analyzing /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/app2.py
2675 INFO: Processing module hooks...
2675 INFO: Loading module hook "hook-encodings.py"...
2725 INFO: Loading module hook "hook-sysconfig.py"...
2735 INFO: Loading module hook "hook-xml.py"...
2939 INFO: Loading module hook "hook-pydoc.py"...
2940 INFO: Loading module hook "hook-distutils.py"...
2957 INFO: Looking for ctypes DLLs
2957 INFO: Analyzing run-time hooks ...
2965 INFO: Looking for dynamic libraries
3039 INFO: Looking for eggs
3039 INFO: Using Python library /Users/barnysanchez/.pyenv/versions/3.7.7/lib/libpython3.7m.dylib
3041 INFO: Warnings written to /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/build/app2/warn-app2.txt
3069 INFO: Graph cross-reference written to /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/build/app2/xref-app2.html
3081 INFO: checking PYZ
3081 INFO: Building PYZ because PYZ-00.toc is non existent
3081 INFO: Building PYZ (ZlibArchive) /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/build/app2/PYZ-00.pyz
3400 INFO: Building PYZ (ZlibArchive) /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/build/app2/PYZ-00.pyz completed successfully.
3405 INFO: checking PKG
3405 INFO: Building PKG because PKG-00.toc is non existent
3405 INFO: Building PKG (CArchive) PKG-00.pkg
5662 INFO: Building PKG (CArchive) PKG-00.pkg completed successfully.
5665 INFO: Bootloader /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/lib/python3.7/site-packages/PyInstaller/bootloader/Darwin-64bit/run
5665 INFO: checking EXE
5666 INFO: Building EXE because EXE-00.toc is non existent
5666 INFO: Building EXE from EXE-00.toc
5666 INFO: Appending archive to EXE /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/dist/app2
5684 INFO: Fixing EXE for code signing /Users/barnysanchez/Documents/GitHub/PROJECT2/app2/dist/app2
5701 INFO: Building EXE from EXE-00.toc completed successfully.
```

> Step 8: Test the install file (under /dist directory)

```
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2 > cd dist
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2/dist > ls -al
total 10536
drwxr-xr-x   3 barnysanchez  staff       96 Mar 26 20:18 .
drwxr-xr-x  11 barnysanchez  staff      352 Mar 26 20:18 ..
-rwxr-xr-x   1 barnysanchez  staff  5393109 Mar 26 20:18 app2
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2/dist > ./app2
Hello world :) !!!
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2/dist >
```

> Step 9: Exit (deactivate) the virtual environment

```
(app2)  barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2/dist > deactivate
barnysanchez@IBM3R > ~/Documents/GitHub/PROJECT2/app2/dist >
```

------------

## E) EXTRA GOODIES AND HIGHLIGHTS

### > About pyinstaller 

https://pyinstaller.readthedocs.io/en/stable/usage.html  (details on usage)

### > Primer on virtual environments (extra read)

https://realpython.com/python-virtual-environments-a-primer/


### > pyenv-virtualenv awesomeness!

One thing I have to highligh is that following the link of option 2 gives some extra functionality like automatically activate/deactive an environment. Again this is the link provided before up in this document:  https://medium.com/python-every-day/python-development-on-macos-with-pyenv-virtualenv-ec583b92934c