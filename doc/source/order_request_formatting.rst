Formatting Order Requests
=========================

:class:`~async_v20.OandaClient` provides the option to format :class:`~async_v20.definitions.types.OrderRequest`'s
in the context of the instrument the OrderRequest is for.

Confused? So was I, Let's have a look at an `instrument`. **series()**
representation of an :class:`~async_v20.definitions.types.Instrument`.

.. code-block:: python
    :emphasize-lines: 2,4-8,11

    display_name                      Brent Crude Oil
    display_precision                               3
    margin_rate                                  0.05
    maximum_order_units                        100000
    maximum_position_size                           0
    maximum_trailing_stop_distance                100
    minimum_trade_size                              1
    minimum_trailing_stop_distance               0.05
    name                                      BCO_USD
    pip_location                                   -2
    trade_units_precision                           0
    type                                          CFD
    dtype: object

The attributes of this instrument outline the formatting of OrderRequests for this instrument.
For instance *Instrument*. **trade_units_precision** defines how many decimal places
:class:`~async_v20.definition.types.OrderRequest`. **units** may be defined for this instrument (our example *0*).

You will also notice **minimum** and **maximum** values for other *OrderRequest*'s attributes.

This is where *OandaClient*. **format_order_requests** changes the behavior of the client.
If **format_order_requests** is set to :class:`True` *OandaClient* will limit values into the
valid range specified by the instrument. By default this feature is disabled.

.. note::
    The precision of :class:`~async_v20.definitions.primitives.DecimalNumber` and
    :class:`~async_v20.definitions.primitive.PriceValue` will **always** be rounded
    to the correct precision for the instrument, regardless of *OandaClient*. **format_order_requests**
    value.


**Example:**

.. code-block:: python
    :emphasize-lines: 6-8,11,15

    >>> from async_v20 import OandaClient
    >>> import asyncio
    >>> client = OandaClient()
    >>> run = loop.run_until_complete
    >>> run(client.create_order('AUD_USD', 0))
    Traceback (most recent call last):
    ValueError: OrderRequest units 0.0 are less than the minimum trade size 1.0
    >>> run(client.create_order('AUD_USD', 1))
    <Status [201]: orderCreateTransaction, orderFillTransaction, relatedTransactionIDs, lastTransactionID>
    >>> client.format_order_requests
    False
    >>> client.format_order_requests = True
    >>> run(client.create_order('AUD_USD', 0))
    <Status [201]: orderCreateTransaction, orderFillTransaction, relatedTransactionIDs, lastTransactionID>
    >>> client.format_order_requests = False
    >>> run(client.create_order('AUD_USD', 1, trailing_stop_loss_on_fill=0))
    Traceback (most recent call last):
    ValueError: Trailing stop loss distance is not 0.0005 < 0.0 < 1.0
    >>> client.format_order_requests = True
    >>> run(client.create_order('AUD_USD', 1, trailing_stop_loss_on_fill=0))
    <Status [201]: orderCreateTransaction, orderFillTransaction, relatedTransactionIDs, lastTransactionID>