==================================
Welcome to icyeye's documentation!
==================================

.. topic:: Overview

    :mod:`icyeye` provides functionality for parsing CSS files (or any other file
    with style related information) to detect image declaration and encode these
    images into a copy of the original file.
    
    This process cuts down on the HTTP overhead of having many small image files
    being requested from a CSS file by sending the data in one go.

Installing
----------

:mod:`icyeye` can be installed with ``easy_install``:

.. code-block:: bash 

    $ easy_install icyeye

or ``pip``:

.. code-block:: bash

    $ pip icyeye

If you don't have access to ``easy_install`` or ``pip`` then you can run the
``setup.py`` script. To get a code checkout from the git repo, do:

.. code-block:: bash 

    $ git clone git://github.com/euangoddard/icyeye.git
    
Then you can run ``setup.py``:

.. code-block:: bash 

    $ python setup.py install

.. note:: If you are installing system wide you will need to do this as root.

Building this documentation
---------------------------

This documentation can be built using `Sphinx <http://sphinx.pocoo.org/>`_. Get
a copy of Sphinx on your make and then in the docs directory, do:

.. code-block:: bash

    $ make html

Usage overview
--------------

After installing :mod:`icyeye` you can either use it in a python project of
your own, via the interactive python interpreter, or via the command line
interface (CLI).

To invoke a conversion via the CLI do, e.g., the following:

.. code-block:: bash

    $ icyeye ~/dev/web/project/main.css /main.css /tmp/inline-images.css

For more advanced usage, see the :doc:`narrative`.

Dependencies and python version
-------------------------------

:mod:`icyeye` has no external dependencies as all functionality is taken from
the python standard library.

:mod:`icyeye` was written using python 2.5 and should be forward compatible to
version 2.7. It relies on :func:`functools.partial`. The :mod:`functools` was
introduced in python 2.5, although there is a
`backport <http://pypi.python.org/pypi/functools25/2.5>`_ for python 2.4
available on pypi. 

Sections
--------

.. toctree::
   :maxdepth: 2
   
   api
   narrative

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

