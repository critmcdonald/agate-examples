Rounding
========

Because **agate** used the more precise Decimal data type, rounding numbers is a pain, frankly, because you can't use the simple `round` method. You have to use the `.compute` method. There is a [an explanation in the agate docs](http://agate.readthedocs.io/en/1.6.0/cookbook/compute.html#round-to-two-decimal-places), but here are some more examples.

## Basic rounding

This is the simplest example of rounding using the `.compute` method. We are creating a new column called `rounded_column` based on decimal column in our table, called `col_to_round` in this case.

You first have to import the python Decimal packate

``` python
# you need to import this package first
from decimal import Decimal
```
Then you create a new column by calling the orginal and extending it with `.quantize(Decimal('0.01')`.

``` python
# create new table with new column
table_with_round = table_name.compute([
    ('rounded_column', # name of new column
      agate.Formula(
        agate.Number(), #data type for formula
        lambda row: row['col_to_round'].quantize(Decimal('0.01') #quantize to two places
      )
    ))
])
```

To round to the tenth, use `Decimal('0.1')`.

## Round as part of a function

In this case, we extrapolate the lambda self-contained function into a defined function so we can include some other math. The `make_percentage` function takes the value of the called column (Hispanic population in our case) and divides it by another column (Total population) and then rounds it. We can then use that to compute multiple columns (which we did with Not Hispanic).

``` python
def make_percentage(row):
    return lambda r: ((r[row] / r['Total pop']) * 100).quantize(Decimal('0.01'))

percentages = raw.compute([
    ('Hispanic Percent', agate.Formula(agate.Number(), make_percentage('Hispanic'))),
    ('Not Hispanic Percent', agate.Formula(agate.Number(), make_percentage('Not Hispanic'))),
])
```
