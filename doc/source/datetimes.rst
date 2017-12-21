Dealing With Time
=================

DateTimes in async_20 have a requirement to support two time formats:

    - RFC3339   *'2017-08-11T15:04:31.639182000Z'*
    - UNIX      *'1502463871.639182000'*

.. note::
    These are the two valid arguments that may be supplied to :class:`~async_v20.OandaClient`.

:class:`~async_v20.definitions.primitives.DateTime` is responsible for handling datetimes in async_v20
**DateTime** creates *pandas*. **Timestamp**'s from either the RFC3339 or UNIX format.

async_v20 adds an additional helper method *pandas*. *Timestamp*. **json** for the purpose
of serializing to the correct JSON format OANDA expects.

**Serializing**

.. code-block:: python

    from async_v20 import DateTime
    unix_example = '1502463871.639182000'
    rfc3339_example = '2017-08-11T15:04:31.639182000Z'
    dt = DateTime(unix_example)
    dt
    Timestamp('2017-08-11 15:04:31.639182+0000', tz='UTC')
    dt.json('RFC3339')
    '2017-08-11T15:04:31.639182000Z'
    dt.json('UNIX')
    '1502463871.639182000'
    dt.json('UNIX') == unix_example
    True
    dt = DateTime(rfc3339_example)
    dt.json('UNIX')
    '1502463871.639182000'
    dt.json('UNIX') == unix_example
    True

**Creating from time.time()**

.. code-block:: python

    from async_v20 import DateTime
    from time import time
    dt = DateTime(time())
    dt
    Timestamp('2017-12-21 01:22:37.762530+0000', tz='UTC')
    dt.json('UNIX')
    '1513819357.762530000'
    dt.json('RFC3339')
    '2017-12-21T01:22:37.762530000Z'

**Creating from datetime.datetime.now()**

.. code-block:: python

    from async_v20 import DateTime
    from datetime import datetime
    dt = DateTime(datetime.now())
    dt
    Timestamp('2017-12-21 12:31:03.982327')


**DataFrame**

.. code-block:: python

    from async_v20 import OandaClient
    import asyncio
    loop = asyncio.get_event_loop()
    run = loop.run_until_complete
    client = OandaClient()
    rsp = run(client.get_candles('EUR_USD'))
    df = rsp.candles.dataframe()
    df.time[0]
    Timestamp('2017-12-20 23:30:40+0000', tz='UTC')
    df = rsp.candles.dataframe(datetime_format='RFC3339')
    df.time[0]
    '2017-12-20T23:30:40.000000000Z'
    df = rsp.candles.dataframe(datetime_format='UNIX')
    df.time[0]
    1513812640000000000
    type(df.time[0])
    # <class 'numpy.int64'>
    df = rsp.candles.dataframe(json=True, datetime_format='UNIX')
    df.time[0]
    '1513812640.000000000'
    type(df.time[0])
    # <class 'str'>