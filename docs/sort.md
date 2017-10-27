Sorting
========

## Simple sort

```python
new_table = table.order_by('birth_date', reverse=True)
```

## Sort by multiple columns

This is in the [agate docs](http://agate.readthedocs.io/en/1.6.0/cookbook/sort.html?highlight=order_by#multiple-columns), but ...

```python
sorted_table = data.order_by(lambda row: (row['county'], row['city']))
```

The new `sorted_table` will be ordered by `county`, then `city`.