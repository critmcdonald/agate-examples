Imports & Exports
=================

To do:

* Create a data example that can be used throughout. Or a small set of them.
* add zfill example from schools, or link to it if elsewhere
* add from xlsx hints if any 
* add a date example for setting column types.
* figure out a loop of imports for a series of files that are similar except for an id or filename. So I can update the list of IDs or names and they would all import, then I can `.merge()` them together.


## From csv

I'm usually pulling from .csv files and it's pretty normal.::

```python
  table = agate.Table.from_csv('filename.csv')
```

## Set column type

But more often than not I have to explicitly set a certain column::

  specified_types = {
      'column_name_one': agate.Text(),
      'column_name_two': agate.Number()
  }

  table = agate.Table.from_csv('filename.csv', column_types=specified_types)

I need a date example here.

## Fill in zero-padded fields

This is covered in the [compute section](compute.md#zfill-fix-zero-padded-ids).

## Add timezone to a date

I had a case where my original data was in UTC time, but I needed to convert it to Central time. According to [agate docs on dates](), it imports naive of timezone, so you have to set it to a specific one before you can convert it to another:

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

In this case, the column name for the date in my data was "dateTime", not to be confused with the `.DateTime` data type used in the definition.

See the [compute page](compute.md#converting-timezones) for the conversion.

## Import from json

The example above also shows importing from a `.json` file::

``` python
    flow_08154700 = agate.Table.from_json('../downloads/flow-08154700.json', column_types=specified_type, key='records')
```

Note you have to have a key, which is the part of the json file that defines the list of records (notably called "records" in that instance. 

My data looked like this::

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

But it was only the "records" nodes that I needed, so the value I needed for the key was `records`. I'm not even sure how I would get the other information at the top.

