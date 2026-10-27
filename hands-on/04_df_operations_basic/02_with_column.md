### .withColumn("new_col_name" , value_in_it )

- dataframes are immutable , if u apply 1 method on it , then new dataframe is returned

Task : along with all the column of employees we want to show their 10% incremented salary as well 

```python
option 1:

df_1 = df.select( * , df.salary*1.10.alias("incremented_salary"))
df_1.show()
df.show()

option 2:

df_2 = df.withColumn("incr_salary", df.salary*1.10)
df_2.show()  
# displays all the columns along with new column as incr_salary 


## if u use the existing col name in .withColumn() then that col will be updated in new df
df2 = df.withColumn("salary" , df.salary*1.10)

```

### Imp General point :

```python
df created using some file 

df = df.select().filter()...

now the df variable will start referencing to new dataframe logic, the link to previous dataframe will vanish, as 1 variable can point to only 1 variable !! so the latest one is the valid one

```



