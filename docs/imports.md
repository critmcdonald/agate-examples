Imports & Exports
=================

> To do:

> * add xlsx hints, if any 
> * add a loop of imports for a series of files that are similar except for an id or filename. So I can update the list of IDs or names and they would all import, then I can `.merge()` them together.


## Set column type

More often than not I have to set some of the columns to a specific data type when importing. An example might be to ensure a FIPS code or ZIP is considered Text instead of a Number:

``` python
  specified_types = {
      'column_name_one': agate.Text(),
      'column_name_two': agate.Number()
  }

  table = agate.Table.from_csv('filename.csv', column_types=specified_types)
```

## Add timezone to a date

I had a case where my original data was in UTC time, but I needed to convert it to Central time. According to [agate docs on dates](http://agate.readthedocs.io/en/1.6.0/cookbook/datetime.html), it imports dates naive of timezone, so you have to set it to a specific timezone before you can convert it to a different one:

``` python
  import pytz

  ## sets date coming in at UTC so we can convert it later
  specified_type = {
      'dateTime': agate.DateTime(timezone=pytz.utc)
  }

  # import all the file.
  flow_08154700 = agate.Table.from_json('../downloads/flow-08154700.json', column_types=specified_type, key='records')
```

Note you need to import the [pytz library](http://pytz.sourceforge.net/index.html?highlight=list%20timezones#). You can get a [list timezones here](https://stackoverflow.com/questions/13866926/python-pytz-list-of-timezones) (or make your own).

In this case, the column name for the date in my data was "dateTime", not to be confused with the `.DateTime()` data type used in the definition.

See the [compute page](compute.md#converting-timezones) for the conversion.

## Import from json

The example above also shows importing from a `.json` file::

``` python
    flow_08154700 = agate.Table.from_json('../downloads/flow-08154700.json', column_types=specified_type, key='records')
```

Note you need a key, which is the part of the json file that defines the list of records (notably called "records" in this instance). 

My data looked like this:

    {
        "siteId":"08154700",
        "siteNumber":"08154700",
        "siteName":"Bull Ck at Loop 360 nr Austin, TX",
        "bankFullStage":5,
        "value1Type":"Stage",
        "value2Type":"Flow",
        "records":[
            {
                "dateTime":"2017-08-27T03:00:00Z",
                "value1":4.46,
                "value1Qualifier":"P",
                "value2":414.00,
                "value2Qualifier":"P"
            },
            {
                "dateTime":"2017-08-27T02:55:00Z",
                "value1":4.38,
                "value1Qualifier":"P",
                "value2":383.00,
                "value2Qualifier":"P"
        }
    ]}

I only needed the "records" nodes for my table, so the value I needed for the key was `records`. I'm not even sure how I would get the other information at the top.

