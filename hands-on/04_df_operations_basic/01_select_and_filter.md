## ways to refer to columns in pyspark df :

### First we need to create a temp view from the df and then we can SQL on this temp view 

- df.createOrReplaceTempView("emp_view")

- expr() can be used in .select , .withColumn() , etc 
- expr() just allows to use the sql strings we waanna use 

### 1) .select()

- there are 2 syntaxes : dataframe code and sql code

1) string based approach :

```python
df.select("eid","name","dept").show(2)

```

2) col() based approach

```python
from pyspark.sql.functions import col

df.select(col("eid"), col("name")).show()

```

3) attribute based

```python

df.select(df.eid, df.name, df.salary).show()

```

## if all do same thing why do we have 3 ways ??

scenario : while showing salary u wanna multiply it by 1.10 and then display results

so here the string based approach will fail, but the others will work fine !!

df.select(col("name"), col("salary")*1.10, df.xyz*1.20).show()  this works ✅ (can mix n match ways)
df.select("name" , "salary"*1.10).show() 👎

- **so keep in mind , that when u want to apply some transformation on SELECT columns and display stuff , then use the col() object based method**

--------------------------------------------------------------------------------------------

### 2) selectExpr() : 

```python

df.selectExpr("id", "name" , "salary * 1.10 as incr_salary" ).show(3)  This works ✅

```

.select() and expr imported function combo :

from pyspark.sql.functions import expr

df.select("name" , expr("SALARY /12 AS monthly_salary")).show()
- use the above when u want to use .selectExpr() way but use want to use .select() only

---------------------------------------------------------------------------------------


Q) u have yearly salary of employees and want to see monthly salary of employees

df.select((df.salary/12).alias("monthly_salary"), "name", "emp_id").show()

spark.sql("SELECT salary / 12 AS monthly_salary from emp_view").show()

---------------------------------------------------------------------------------------


### 3) .where() and .filter() :

- filters out the records based on condition u provide 

df.where(" dept == 'Tech' AND salary>50000 ").show()   : displays all columns for these records

df.where( (col("dept")=="Tech") & (col("salary")>50000) ).show()

df.filter( (df.dept == "HR") | (df.salary > 50000) ).show()


- both .where() and .filter() does the same thing, no difference at all

-------------------------------------------------------------------------------------------

### general eg

```python 

df.select("eid", "ename").where(df.dept == "HR").show()  can use .filter() as well here

df.selectExpr("ename", "dept", "date_of_joining").where("salary > 75000 AND dept = 'Marketing'").show()

df.select("ename", "salary").filter((df.date_of_joining < "2010-01-01") & (df.salary < 80000)).show()

df.selectExpr("ename", "salary").where("date_of_joining < '2010-01-01' AND salary < 80000").show()

spark.sql("SELECT ename, salary > (SELECT AVG(salary) FROM employee) as above_avg_salary FROM employee").show()

spark.sql("SELECT ename, (YEAR(current_date()) - YEAR(date_of_joining) <= 10) as joined_last_decade FROM employee").show()

```

-------------------------------------------------------------------------------------------



### order of the methods while working on dataframes matters :

- each method on dataframe returns a new dataframe, so u can only do that much stuff which the previous dataframe has provided u in current method 

```python
# Works: filter first, then pick columns
df.filter("dept = 'Finance'").select("ename")  after .filter() we still have access to all the columns therefore we can select ename 

# Also works (Exercise 12): select keeps dept, so filter can still use it
df.select("ename", "dept").filter("dept != 'Tech'")

# Fails: select dropped dept, so filter can't find it
df.select("ename").filter("dept != 'Tech'")  ❌

```

------------------------------------------------------------------------------------------

