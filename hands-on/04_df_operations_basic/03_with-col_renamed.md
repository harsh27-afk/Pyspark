### .withColumnRenamed("old_col_name", "new_name")


- Rename the col name from dept TO department and date_of_joining to DOJ

```python

df.select("id", "name" , df.dept.alias("department"), col("date_of_joining").alias("DOJ")).show()

```

### The actual way to rename columns correctly :

```python

df1 = df.withColumnRenamed("dept", "department")/
        .withColumnRenamed("date_of_joining", "DOJ")

# here no need to mention all the columns and all, just the ones u want to change 

```