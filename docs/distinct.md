Distinct values
===============

Sometimes you need to see distinct values in a column or filter a table based on distinct values.

## Distinct values of a column

If you want to see the distinct values of a column, you can call the `.values_distinct()` method on that column and see the results in a tuple.

``` python
rated.columns['C_RATING'].values_distinct()
```

`rated` is the name of the table in this case, and `C_RATING` is the column. The result looks like this:

```
('A', 'I', 'X', 'Z', 'T', 'M')
``` 

## Filter a table based on distinct values

In this case I started with table (`tec`) of campaign contributions over multiple years. Candidates (and their race) are listed more than once. I wanted to get the demographics of the group, so I had to first get a table that listed each candidate only once. I selected the two columns I want (Candidate and Race-Ethnicity) and then used the `distinct` method using the `Candidate` to get the new table.
)

``` python
candidates = tec.select(['Candidate', 'Race-Ethnicity']).distinct('Candidate')
```

Now the new `candidates` table only lists each candidate once.

(NOTE: If a candidate was listed more than once with a different race, the table would keep only the first value. Not a problem with race, but could bite you in other cases. I did try to pass in `['Candidate', 'Race-Ethnicity']` but the result was NOT distinct.)