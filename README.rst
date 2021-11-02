Jaeger (tcp) client for AsyncIO
===============================

.. image:: https://github.com/alvassin/async-jaeger/workflows/Unit%20Tests/badge.svg?branch=master
   :target: https://github.com/alvassin/async-jaeger/actions?query=branch%3Amaster
   :alt: Unit tests

.. image:: https://coveralls.io/repos/github/alvassin/async-jaeger/badge.svg?branch=master
   :target: https://coveralls.io/github/alvassin/async-jaeger
   :alt: Coverage

.. image:: https://img.shields.io/pypi/v/async-jaeger.svg
   :target: https://pypi.python.org/pypi/async-jaeger/
   :alt: Latest Version

.. image:: https://img.shields.io/pypi/wheel/async-jaeger.svg
   :target: https://pypi.python.org/pypi/async-jaeger/

.. image:: https://img.shields.io/pypi/pyversions/async-jaeger.svg
   :target: https://pypi.python.org/pypi/async-jaeger/

.. image:: https://img.shields.io/pypi/l/async-jaeger.svg
   :target: https://pypi.python.org/pypi/async-jaeger/


Client-side library that can be used for distributed trace collection from
Python apps via TCP (HTTP) to Jaeger.

See the `OpenTracing Python API`_ for additional detail.

Installation
------------

.. code-block:: bash

    pip3 install async-jaeger

Debug Traces (Forced Sampling)
------------------------------

Programmatically
~~~~~~~~~~~~~~~~

The OpenTracing API defines a `sampling.priority` standard tag that
can be used to affect the sampling of a span and its children:

.. code-block:: python

    from opentracing.ext import tags as ext_tags

    span.set_tag(ext_tags.SAMPLING_PRIORITY, 1)

Via HTTP Headers
~~~~~~~~~~~~~~~~

Jaeger Tracer also understands a special HTTP Header `jaeger-debug-id`,
which can be set in the incoming request, e.g.

.. code-block:: bash

    curl -H "jaeger-debug-id: some-correlation-id" http://myhost.com


When Jaeger sees this header in the request that otherwise has no
tracing context, it ensures that the new trace started for this
request will be sampled in the "debug" mode (meaning it should survive
all downsampling that might happen in the collection pipeline), and
the root span will have a tag as if this statement was executed:

.. code-block:: python

    span.set_tag('jaeger-debug-id', 'some-correlation-id')

This allows using Jaeger UI to find the trace by this tag.

License
-------
`Apache 2.0 License`_.

.. _Apache 2.0 License: https://github.com/alvassin/async-jaeger/blob/master/LICENSE
.. _OpenTracing Python API: https://github.com/opentracing/opentracing-python
