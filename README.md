[![Build Status][ci-img]][ci] [![Coverage Status][cov-img]][cov] [![PyPI Version][pypi-img]][pypi] [![Python Version][pythonversion-img]][pythonversion] [![FOSSA Status][fossa-img]][fossa]

# Jaeger bindings for Python AsyncIO

This is a client-side library that can be used to instrument Python apps
for distributed trace collection, and to send those traces to Jaeger.
See the [OpenTracing Python API](https://github.com/opentracing/opentracing-python)
for additional detail.

```bash
pip install async-jaeger
```

## Getting Started

```python
import asyncio
import logging
from async_jaeger.reporter import HttpReporter
from async_jaeger import Tracer


async def main():
    logging.basicConfig(format='%(asctime)s %(message)s', level=logging.DEBUG)

    tracer = Tracer(
        service_name='your-app-name',
        reporter=HttpReporter('http://localhost:14268/api/traces')
    )

    with tracer.start_span('TestSpan') as span:
        span.log_kv({'event': 'test message', 'life': 42})

        with tracer.start_span('ChildSpan', child_of=span) as child_span:
            child_span.log_kv({'event': 'down below'})

    await tracer.close()

if __name__ == "__main__":
    asyncio.run(main())
```

## Debug Traces (Forced Sampling)

### Programmatically

The OpenTracing API defines a `sampling.priority` standard tag that
can be used to affect the sampling of a span and its children:

```python
from opentracing.ext import tags as ext_tags

span.set_tag(ext_tags.SAMPLING_PRIORITY, 1)
```

### Via HTTP Headers

Jaeger Tracer also understands a special HTTP Header `jaeger-debug-id`,
which can be set in the incoming request, e.g.

```sh
curl -H "jaeger-debug-id: some-correlation-id" http://myhost.com
```

When Jaeger sees this header in the request that otherwise has no
tracing context, it ensures that the new trace started for this
request will be sampled in the "debug" mode (meaning it should survive
all downsampling that might happen in the collection pipeline), and
the root span will have a tag as if this statement was executed:

```python
span.set_tag('jaeger-debug-id', 'some-correlation-id')
```

This allows using Jaeger UI to find the trace by this tag.

## License

[Apache 2.0 License](./LICENSE).

[ci-img]: https://github.com/jaegertracing/jaeger-client-python/workflows/Unit%20Tests/badge.svg?branch=master
[ci]: https://github.com/jaegertracing/jaeger-client-python/actions?query=branch%3Amaster
[cov-img]: https://codecov.io/gh/jaegertracing/jaeger-client-python/branch/master/graph/badge.svg
[cov]: https://codecov.io/gh/jaegertracing/jaeger-client-python
[pypi-img]: https://badge.fury.io/py/jaeger-client.svg
[pypi]: https://badge.fury.io/py/jaeger-client
[pythonversion-img]: https://img.shields.io/pypi/pyversions/jaeger-client.svg
[pythonversion]: https://pypi.org/project/jaeger-client
[fossa-img]: https://app.fossa.io/api/projects/git%2Bgithub.com%2Fjaegertracing%2Fjaeger-client-python.svg?type=shield
[fossa]: https://app.fossa.io/projects/git%2Bgithub.com%2Fjaegertracing%2Fjaeger-client-python?ref=badge_shield
