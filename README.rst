.. image:: https://d2xtrvzo9unrru.cloudfront.net/brands/smartfile/logo.png
   :alt: SmartFile

A `SmartFile`_ Open Source project. `Read more`_ about how SmartFile
uses and contributes to Open Source software.


Summary
------------

This is an API library (Java) for `SmartFile`_. This library includes two API
clients. Each one represents one of the supported authentication methods.
``BasicClient`` is used for HTTP Basic authentication, using an API key and
password. ``OAuthClient`` is used for OAuth (version 1) authentication,
using tokens, which will require user interaction to complete authentication
with the API.

Both clients provide a thin wrapper around an HTTP library, taking care of some
of the mundane details for you. The intended use of this library is to refer to
the API documentation to discover the API endpoint you wish to call, then use
the client library to invoke this call.

SmartFile REST API information is available at the
`SmartFile developer site <https://app.smartfile.com/api/>`_.


Installation
------------

via source code / GitHub.

::

    $ git clone https://github.com/smartfile/client-java.git

More information is available at `GitHub <https://github.com/smartfile/client-java>`_

Usage
-----

Choose Basic authentication method, then continue to use the SmartFile API.
OAuth authentication is still under development. See Known Issues below.

Some of the details this library takes care of are:

* Encoding and decoding of parameters and return values. You deal with Java
  types only.
* URLs, using the API version, endpoint, and object ID, the URL is created for
  you.
* Authentication. Provide your API credentials to this library, it will take
  care of the details.

Basic Authentication
--------------------

sample Java code::

       import com.smartfile.api.*;
       BasicClient api = new BasicClient("**********", "**********");
       api.setApiUrl("app.smartfile.com");
       String result = responseToString(api.get("/ping"));
       System.out.println("Ping test:");
       System.out.println(result);


OAuth Authentication
--------------------

Authentication using OAuth authentication is bit more complicated,
 as it involves tokens and secrets. See Known Issues below.


Calling endpoints
-----------------

Once you instantiate a client, you can use the get/put/post/delete methods
to make the corresponding HTTP requests to the API. There is also a shortcut
for using the GET method, which is to simply invoke the client.


Some endpoints accept an ID, this might be a numeric value, a path, or name,
depending on the object type. For example, a user's id is their unique
``username``. For a file path, the id is it's full path.

You should start by using the ping example above. The ping endpoint does not
require authentication and you can verify network connectivity to the api is
working. Second call an endpoint such as whoami below. This will verify you
can call a simple GET api endpoint with authentication.

sample Java code::

       import com.smartfile.api.*;
       BasicClient api = new BasicClient("**********", "**********");
       api.setApiUrl("app.smartfile.com");
       String result = responseToString(api.get("/whoami"));
       System.out.println("Whoami test:");
       System.out.println(result);


File transfers
--------------

Once you have verified network connectivity and authentication, you are ready
for more complex api operations. Uploading and downloading files is supported.

To upload a file::

    import com.smartfile.api.*;
    BasicClient api = new BasicClient("**********", "**********");
    api.setApiUrl("app.smartfile.com");
    // @TODO


Downloading is automatic, if the ``'Content-Type'`` header indicates
content other than the expected JSON return value, then a file-like object is
returned.


To download a file::

    import com.smartfile.api.*;
    BasicClient api = new BasicClient("**********", "**********");
    api.setApiUrl("app.smartfile.com");
    InputStream in = api.get("/path/data","/YourFolder/remote-file.txt");
    FileOutputStream out = new FileOutputStream("local-file.txt");
    int c;
    while ((c = in.read()) != -1) {
        out.write(c);
    }
    out.close();


Tasks
-----

Operations are long-running jobs that are not executed within the time frame
of an API call. For such operations, a task is created, and the API can be used
to poll the status of the task.


Synchronization
---------------

If you have many files that you wish to keep synchronized between a number of
computer systems and SmartFile, the sync API can help. The sync API is an
implementation of the excellent and popular rsync delta algorithm. It is
completely compatible with the file formats used in librsync version 0.9.7.

The `Rsync algorithm`_ provides a means to synchronize two files by transferring
just the parts that differ, while retaining the parts that are the same. This
allows files to be quickly and efficiently synchronized. The rsync algorithm
is very popular and widely deployed. The implementation in librsync is very
high quality Open Source software.

Once you have librsync available, synchronizing files using the SmartFile sync
API is very simple. The API exposes three calls, corresponding to the three
steps of the algorithm.

1. Signature (destination)
2. Delta (source)
3. Patch (destination)

Depending on the direction of synchronization, source and destination may be
either your local machine or the SmartFile API. In either case, the steps are
performed in the same order.

The SmartFile API client provides a simple ``SyncClient`` class that
demonstrates synchronizing files in either direction. An example of it's usage
follows.


.. _SmartFile: http://www.smartfile.com/
.. _Read more: http://www.smartfile.com/open-source.html
.. _Rsync algorithm: http://en.wikipedia.org/wiki/Rsync#Algorithm


Known Issues
------------

* OAuth in the Java client is still under development; use an API Key (basic authentication) at this time.

