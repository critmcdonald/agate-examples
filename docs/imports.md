Imports & Exports
=================

## Set column type

More often than not I have to set some of the columns to a specific data type when importing. An example might be to ensure a FIPS code or ZIP is considered Text instead of a Number:

``` python
specified_types = {
    'column_name_one': agate.Text(),
    'column_name_two': agate.Number()
}

table = agate.Table.from_csv('filename.csv', column_types=specified_types)
```

## Import multiple files and merge

In this case, I had a csv file for each club. I wanted to import and combine them into a single file. For this to work, all the files have to be the same columns/types:

``` python
# Names of my files/clubs
club_names = [
    'cheerup_charlies',
    'empire',
    'mohawk',
    'sidewinder',
    'stubbs',
]
# Initiate list of tables that will be generated in the import loop
club_tables = []

# Loop to import the five files, then set them up to append
for club in club_names:
    club_file = "../data-orig/" + club + ".csv"
    club_table = agate.Table.from_csv(
      club_file,
      column_types=specified_types #not listed here
    )
    club_tables.append(club_table)

# Merge the tables collected in club_tables during the loop
combo = agate.Table.merge(club_tables)
```

## Set date format

```python
specified_types = {
    'date_col': agate.Date('%Y%m%d'),
}
```
And set the value inside `.Date()` based on [strftime formats](http://strftime.org/).

## Add timezone to a date

I had a case where my original data was in UTC time, but I needed to convert it to Central time. According to [agate docs on dates](http://agate.readthedocs.io/en/1.6.0/cookbook/datetime.html), it imports dates naive of timezone, so you have to set it to a specific timezone before you can convert it to a different one:

``` python
import pytz

## sets date coming in at UTC so we can convert it later
specified_type = {
    'dateTime': agate.DateTime(timezone=pytz.utc)
}

# import all the file.
flow_08154700 = agate.Table.from_json(
    '../downloads/flow-08154700.json',
    column_types=specified_type, key='records'
)
```

Note you need to import the [pytz library](http://pytz.sourceforge.net/index.html?highlight=list%20timezones#). You can get a [list timezones here](https://stackoverflow.com/questions/13866926/python-pytz-list-of-timezones) (or make your own).

In this case, the column name for the date in my data was "dateTime", not to be confused with the `.DateTime()` data type used in the definition.

See the [compute page](compute.md#converting-timezones) for the conversion.

## Import from json

The example above also shows importing from a `.json` file::

``` python
flow_08154700 = agate.Table.from_json(
    '../downloads/flow-08154700.json',
    column_types=specified_type, key='records'
)
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

