Rounding
========

Rounding numbers is a pain, frankly. You have to use the `.compute` function. There is a bit of [an explanation in the agate docs](), but here are some examples.

## Basic rounding

This is a little simpler than the docs as we do the rounding in the compute function, and it is all we are doing. We are creating a new column called `rounded_column` based on column in our table, called `col_to_round` in this case.


``` python
# you need to import this package first
from decimal import Decimal

table_with_round = table_name.compute([
    ('rounded_column', # name of new column
      agate.Formula(
        agate.Number(), #data type for formula
        lambda row: row['col_to_round'].quantize(Decimal('0.01') #quantize each row
        )
    ))
])
```

## Round as part of a function

THIS MAY NEED MORE EXPLANATION

We extrapolate the lambda function so we can divide by another column first.

``` python
def make_percentage(row):
    return lambda r: ((r[row] / r['Total pop']) * 100).quantize(Decimal('0.01'))

percentages = raw.compute([
    ('Hispanic %', agate.Formula(agate.Number(), make_percentage('Hispanic'))),
])


```