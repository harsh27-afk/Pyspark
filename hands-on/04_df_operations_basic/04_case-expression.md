## Case expressions :

- same as the sql case expressions

1) structure transformation :

```python
from pyspark.sql.functions import when,col

df1 = df.withColumn("incr_salary",
                    when(df.dept == "HR"), df.salary* 1.10)
                    .when(df.dept == "Tech"), df.salary* 1.30)
                    .when(df.dept == "Sales"), df.salary* 1.05)
                    .otherwise(col("salary"))
                    )

These conditions will be applied to each and every row of the old dataframe
if some condition is true the below conditions wont be even checked !! 
and all of these values will go into the new column "incr_salary"


```

2) Literal SQL :

```python 

spark.sql("""SELECT *, CASE 
                     WHEN dept = 'HR' Then salary*1.10
                     WHEN dept = 'Tech' Then salary*1.20
                     ELSE salary
                     END AS incr_salary 
                     FROM employees
            """
        ).show()

```