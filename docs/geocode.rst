=========
Geocoding
=========

Single address geocoding
========================

Import the API client and ensure you have a valid API key::

    >>> from geocodio import GeocodioClient
    >>> client = GeocodioClient(MY_KEY)
    >>> geocoded_location = client.geocode("42370 Bob Hope Drive, Rancho Mirage CA")

The result from the Geocod.io service is a dictionary including your query, its
components, and a list of matching results::

    >>> geocoded_location
    {
      "input": {
        "address_components": {
          "number": "42370",
          "street": "Bob Hope",
          "suffix": "Dr",
          "city": "Rancho Mirage",
          "state": "CA"
        },
        "formatted_address": "42370 Bob Hope Dr, Rancho Mirage CA"
      },
      "results": [
        {
          "address_components": {
            "number": "42370",
            "street": "Bob Hope",
            "suffix": "Dr",
            "city": "Rancho Mirage",
            "state": "CA",
            "zip": "92270"
          },
          "formatted_address": "42370 Bob Hope Dr, Rancho Mirage CA, 92270",
          "location": {
            "lat": 33.738987255507,
            "lng": -116.40833849559
          },
          "accuracy": 1
        },
        {
          "address_components": {
            "number": "42370",
            "street": "Bob Hope",
            "suffix": "Dr",
            "city": "Rancho Mirage",
            "state": "CA",
            "zip": "92270"
          },
          "formatted_address": "42370 Bob Hope Dr, Rancho Mirage CA, 92270",
          "location": {
            "lat": 33.738980796909,
            "lng": -116.40833917329
          },
          "accuracy": 0.8
        }
      ]
    }

This returned several geolocation results in descending order of accuracy, so
the first and most accurate geocoded location latitude and longitude can be
accessed using the `coords` attribute::

    >>> geocoded_location.coords
    (-116.40833849559, 33.738987255507)

.. note::

    To make working with `other geographic data
    <http://postgis.net/docs/ST_Point.html>`_ formats easier the `coords`
    method returns a tuple in (longitude, latitude) format.

Batch geocoding
===============

You can also geocode a list of addresses::

    >>> geocoded_addresses = geocodio.geocode(['1600 Pennsylvania Ave, Washington, DC',
            '3101 Patterson Ave, Richmond, VA, 23221'])

Return just the coordinates for the list of geocoded addresses::

    >>> geocoded_addresses.coords
    [(-116.40833849559, 33.738987255507), (-116.40833849559, 33.738987255507)]

Lookup an address by formatted address::

    >>> geocoded_addresses.get('1600 Pennsylvania Ave, Washington, DC').coords
    (-116.40833849559, 33.738987255507)

Note that to perform the key based lookup you must use the `get` method. This
preserves the list's index based lookup.

.. note::

    If one address cannot be parsed or geocoded the Geocod.io service will
    still respond, but the `response` value for that address will be an error
    message. E.g. if a query was an emptry string, the value for that
    particular query would look like this::

        {
            "query": "",
            "response": {
                "error": "Could not parse address"
            }
        }

    In this case the a lookup for `""` would yield `None`. The `None` value is
    not removed from the list in the `LocationCollection` because then the
    indices in the response addresses would no longer match the indices in the
    request addresses.

Using a Different API Version
=============================

By default, version 1.2 of the Geocod.io library is used. However, you can
access a different Geocod.io API version using the api_version parameter as
seen below::

    >>> from geocodio import GeocodioClient
    >>> client = GeocodioClient(MY_KEY, api_version='v1.3')

.. note::

    The api_version value should be the API version number as it should appear
    in the URL.

.. warning::

    API versions newer than the default may not work if breaking changes have
    been implemented.
