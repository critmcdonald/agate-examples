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

## Rename columns

I often rename columns to make them easier to work with. I typically set up a name list first. The first value is the original name, and the second is the new name:

``` python
# set column names
renamed_columns = {
  'a': 'one',
  'b': 'two',
  'c': 'three'
}

# apply the names
new_table = table.rename(column_names = renamed_columns)
```

## Import from a specific excel sheet

Importing from Excel requires an additional agate package called [agate-excel](http://agate-excel.readthedocs.io/en/0.2.2/), which then has to be imported. This is covered fairly well in the [Cookbook](http://agate.readthedocs.io/en/1.6.0/cookbook/create.html#from-an-excel-spreadsheet).

```python
import warnings
warnings.filterwarnings('ignore')
import agateexcel

data_table = agate.Table.from_xls('file.xls', sheet='data')
# use from_xlsx for newer Excel file types
```

I've gotten a bunch of warning messages the first time I've run code with `agateexcel` about the path of the module, but it still works and warnings do not reappear on subsequent runnings within the same session. But, sometimes I include the import warnings to avoid seeing them.

## Skip lines on import

Sometimes you have titles, descriptions or notes in a spreadsheet before the data starts. You can skip those lines on import

```python
data_table = agate.Table.from_xls('file.xls', skip_lines=2)
```

## Merge files together

If you have two tables with the same columns and datatypes, you can "merge" or stack them on top of each other. You might have to adjust data types if the TypeTester was not consistent on your imports.

``` python
combined = merge([table_one, table_two])
```

## Import multiple files and merge

In this case, I had a csv file for each club and I wanted a loop to import and combine them into a single file. For this to work, all the files have to be the same columns/types:

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

``` json
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
```
I only needed the "records" nodes for my table, so the value I needed for the key was `records`. I'm not even sure how I would get the other information at the top.

