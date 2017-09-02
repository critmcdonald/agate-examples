# Filters: Using .where()

Most of these examples are where I am creating a new table with a subselection of rows from the base table:

``` python
  newtable = oldtable.where(lamda row: row['Column_Name'] == 'something')
```

Lamda is a function that happens just once in place. In the example above, I'm looking through each row, specifically at the column ``Column_Name`` for the text 'something'. If it matches, I keep it and put it in the newtable. If not, it is discarded.

To add here:

* where between dates
* grep
* figure out how to use ``.values_distinct()`` in such a way it makes a nice list.

## Where boolean field is True

Checking if a boolean field is true or false:

``` python
  charters = campus.where(lambda row: row['CFLCHART'] == 1)
```

(Question to self: Test of this will work as ``== True```.)

## Where boolean field is not True


When you want fields that don't match your test.:

``` python
traditional = campus.where(lambda row: row['CFLCHART'] != 1)
```

In this case, we are testing 'CFLCHART' to make sure it does NOT equal 1.

(Note to self: Test if this would catch nulls.)

## By position in string

If you want to look at a specific position in a string and then check it:

``` python
  charters = campus.where(lambda row: row['Campus_ID'][3] == '8')
```

The example is searching the ``Campus_ID`` field looking at the fourth character, checking to see if it is the text ``'8'``.

## Check for blank cells


In this example, I wanted to exclude rows that were null or blank in the `Campus` column:

``` python
  nodistrict = raw.where(lambda row: row['CAMPUS'] is not None)
```

## Filter by date

In this case, I wanted only rows that had a `CentralTime` date on or after August 25. The trick here was importing `datetime`, and setting that reference as below. It wasn't intuitive for me from the docs.:

``` python
    import datetime

    # filter for Aug. 25th or later
    flow_harvey = flow_central.where(
        lambda row: datetime.date(2017, 8, 25) <= row['CentralTime'].date()
        )
```