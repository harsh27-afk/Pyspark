### sorting/order by stuff

**.sort() and .orderBy() do the same thing**

### 1) sort by single column

```python

df1 = df.sort("salary")    sort all the rows in ascending order based on salary column

df2 = df.sort(df.salary.desc())    sorts in descending order based on salary column
df2.show()

from pyspark.sql.functions imposrt desc
df3 = df.sort(desc(df.salary))  this also sorts in descending order based on salary column

```


### 2) sort by multiple columns :

- **The rows will be first sorted by first column and then the rows will be sorted by second column !! THIS MEANS THAT IF SOME ROWS HAD SAME VALUE IN THE FIRST COLUMN FOR THOSE THE SORTING WILL AGAIN HAPPEN AND SECOND COLUMN WILL DECIDE WHICH COLUMN WILL BE WHERE IN THE RANGE OF THE SIMILAR VALUED ROWS**

```python

df.orderBy(df.dept.asc() , df.salary.desc()).show()


df.sort(dept,salary).show()   # both ascending columns


from pyspark.sql.functions import length
df.sort(length(df.ename).desc()).show()


```

- **IF THERE ARE NULLS IN THE SALARY COLUMNS THEN U WILL SEE THOSE ON TOP, BUT U WANT THEM TO BE SEEN IN LAST**

```python
from pyspark.sql.functions import asc_nulls_last
df.sort(df.salary.asc_nulls_last()).show()

```

- **IF THERE ARE NULLS IN THE SALARY COLUMNS AND U TRY TO SORT THEM IN DESC ORDER THEN NULLS WILL APPEAR LAST, BUT U WANNA SEE THEM FIRST**

```python
from pyspark.sql.functions import desc_nulls_first
df.sort(df.salary.desc_nulls_first()).show()

```


### some literal sql around ORDER BY 

```python

spark.sql("SELECT * FROM employees ORDER BY dept , salary DESC").show()

```