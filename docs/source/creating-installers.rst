===========================================
Creating custom installers with constructor
===========================================

Constructor is a tool which allows constructing an installer
for a collection of conda packages. Basically, it creates an
Anaconda-like installer consisting of conda packages.
This tool was previously proprietary and known as cas-installer.
You can install constructor using conda:

``conda install constructor constructor -h``
 
The constructor command takes an installer specification directory
as its argument. This directory needs to contain a ``construct.yaml`` file,
which specifies the name of the installer, the conda channels to pull packages
from, the conda packages included in the installer, etc. The complete list of
keys in this file can be found in ``CONSTRUCT.md``. Also, the
directory may contain some additional optional files such as a
license file, and image files for the Windows installer.
 
An example construct.yaml file
==============================
 
In this example you will see a few of the common keys such as
name (required), version (required), channels, specs,
exclude, and a few others like the key for ``license_file`` and
``welcome_image``. You will also notice that certain packages
in specs are specified for only specific platforms - UNIX, Win.

.. code-block::

    name: Maxiconda
    version: 2.5.5
    install_in_dependency_order: True
    channels:
    - http://repo.continuum.io/pkgs/free/
    specs:
    
    - python 3.5*
    - conda
    - nomkl   	[not win]
    - numpy
    - scipy
    
    - pandas
    - notebook
    - matplotlib
    - lighttpd   	[unix]
    
    exclude:
    - qt
    - sip
    - pyqt
    license_file: EULA.txt
    
    # Welcome image for Windows installer
    welcome_image: photo.png [win]
 
In order to create your custom installer, create a directory
with your ``construct.yaml`` file inside as well as any other
necessary files (EULA.txt, photo.png, etc). While inside the
directory run this command: ``constructor .``

Your installer will be created inside of the directory with
this naming scheme: name-version-yourPlatform.sh/exe.
 
Some additional considerations
==============================
* Constructor does not work with noarch-Python packages.
  All conda packages must be available for the platform you are
  building the installer for.
* An installer created by constructor does not need to include
  conda itself. If you require the ability to use conda after
  installation, add conda to the package list.
* An installer created by constructor is not the same as
  Miniconda. All packages you want to include in the installer
  need to be listed explicitly. In particular, on Windows this
  means that if you want the "Anaconda Prompt", you will have
  to list console_shortcut, as well as menuinst.
* For Windows builds, add the Anaconda channels /free and /msys2
  to the file ``constructor.yaml``. This provides packages such
  as m2w64-toolchain, which is a dependency of theano. It is best
  to add /msys2 as https://repo.anaconda.com/pkgs/msys2/.
