=======================
Narrative documentation
=======================

.. topic:: Overview
    
    This document describes how to use :mod:`icyeye` from another python
    project, via the interactive python interpreter, or via the command line
    interface (CLI).

Functionality :mod:`icyeye` exposes
===================================

:mod:`icyeye` public API comprises two functions:
 - :func:`~icyeye.encode_image_to_base64`
 - :func:`~icyeye.make_css_images_inline`

:func:`~icyeye.encode_image_to_base64`
--------------------------------------

:func:`~icyeye.encode_image_to_base64` takes care of reading an image file and
encoding the data contained therein to the appropriate ASCII-string. The output
will also contain the prefix necessary for use of this string as a ``url`` 
declaration and contain the mime type (determined by the extension of the file),
e.g. if the file ``green.gif`` is a 1x1 px GIF which is green, the following
will be output::

    >>> from icyeye import encode_image_to_base64
    >>> encode_image_to_base64("./green.gif")
    'data:image/gif;base64,R0lGODlhAQABAIAAAAD/AAAAACwAAAAAAQABAAACAkQBADs='
    
.. note:: :func:`~icyeye.encode_image_to_base64` allows any :exc:`IOError` or
    :exc:`OSError` exceptions to propagate.
    
:func:`~icyeye.make_css_images_inline`
--------------------------------------

:func:`~icyeye.make_css_images_inline` can be used to encode all referenced
images within a CSS file (or any other declarations in other files). The
function takes two arguments :data:`css_file_path` and :data:`css_file_url`.

:data:`css_file_path` specifies the path on disk to the file to process.
Absolute and relative paths are supported and any path expansion that can be
performed will be (using :func:`os.path.expanduser`).

:data:`css_file_url` specifies the absolute URL to the file. If all the
``url(...)`` declarations are relative to the style file, :data:`css_file_url`
does not need to be passed.

Absolute URL Examples
~~~~~~~~~~~~~~~~~~~~~

If the style sheet is in a file at ``/home/dev/web/project/style.css`` and the
root of the web server is at ``/home/dev/web/project/``, :data:`css_file_url`
would be ``/style.css``. Likewise, if the style file is at
``/tmp/project/media/css/index.html`` and the root of the web server is at
``/tmp/project/`` :data:`css_file_url` will need to be
``/media/css/index.html``.

Additionally, if you only want to encode images files under a certain size, the
:data:`image_size_limit` keyword argument can be passed. This parameter should
be an integer in bytes. For example, to skip any images greater than 30kB in
size::

    >>> from icyeye import make_css_images_inline
    >>> make_css_images_inline("style.css", image_size_limit=30*1024)
    '<output string>'
    
How references are detected
~~~~~~~~~~~~~~~~~~~~~~~~~~~

:func:`~icyeye.make_css_images_inline` parses the input file looking for
``url(...)`` declarations as specified by the
`W3C guidelines <http://www.w3.org/TR/CSS2/syndata.html#uri>`_. The following
example shows most of the cases that can be detected:

.. code-block:: css

    h1 {
        background: url(blue.jpg);
    }
    h2 {
        background: url('green.gif');
    }
    h3 {
        background: url("red.png");
    }
    h4 {
        background: url(  'pink.png'  );
    }
    h5 {
        background: url(
        "yellow.png"
        );
    }
    
As discussed above, if :data:`css_file_url` is specified absolute and relative
references to image files will be resolved (otherwise, only relative URLs will
be).

.. note:: Presently any references to images stored on remove servers, declared
    with the ``http://`` prefix will be skipped.
    
For example, if the file to be parsed is located at:
``/home/dev/web/project/media/css/style.css`` and the URL to the file is
``/media/css/style.css`` and the contents of ``style.css`` are:

.. code-block:: css

    h1 {
        background: url(../images/green.gif);
    }
    h2 {
        background: url(/media/images/green.gif);
    }
    h3 {
        background: url(http://example.com/green.gif);
    }
    
The following commands the python interpreter can be used... ::

    >>> from icyeye import make_css_images_inline
    >>> output_file = open("/tmp/output.css", "w")
    >>> new_css = make_css_images_inline("/home/dev/web/project/media/css/style.css", "/media/css/style.css")
    >>> output_file.write(new_css)
    >>> output_file.close()

... to generate an output a file, such as:

.. code-block:: css
    
    h1 {
        background: url(data:image/gif;base64,R0lGODlhAQABAIAAAAD/AAAAACwAAAAAAQABAAACAkQBADs=);
    }
    h2 {
        background: url(data:image/gif;base64,R0lGODlhAQABAIAAAAD/AAAAACwAAAAAAQABAAACAkQBADs=);
    }
    h3 {
        background: url(http://example.com/green.gif);
    }
    
.. note:: Any files which cannot be found on disk or cause an :exc:`IOError` or
    :exc:`OSError` will be have the path re-output unchanged in the final file.
    Any other exceptions will be allowed to propagate.
    
The Command Line Interface
==========================

:mod:`icyeye` provides a CLI version of :func:`~icyeye.make_css_images_inline`
so that the python interpreter is not needed. If icyeye is installed on the
system or in a virtualenv, the ``icyeye`` command will be available on the
command-line. Its functionality if best explained by the help switch:

.. code-block:: bash

    $ icyeye --help
    Usage: icyeye [options] <input CSS file> <absolute URL of CSS file> <ouptut file>
    
    Options:
      -h, --help            show this help message and exit
      -m MAX_IMAGE_SIZE, --max-image-size=MAX_IMAGE_SIZE
                            The size (in bytes) which images must be under to be
                            encoded

The only major difference that ``icyeye`` provides in comparison to
:func:`~icyeye.make_css_images_inline` is that it wraps the outputting of the
new CSS file. In addition any exceptions that might occur during normal
operation of the script are excepted and presented to the user as differing
exits codes.