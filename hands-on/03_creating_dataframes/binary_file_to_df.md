### Binary file format like :

pyspark allows to work on following file formats :
- parquet , ORC , avro , Sequence( very old)

- **Serialization and Deserialization :**
- its basically converting in-memory object to a common format and back to in-memory object !!
- parquet is the common file format, when we creaed a dataframe from parquet file, it is being deserialized 


### 1) Creating df from ORC file format :

```python
df = spark.read.orc(r"file:///...")

df.show(3)

- no need to set inferschema as true or provide schema manually BECAUSE THE SCHEMA FOR THE DATA IS PRESENT IN THE FILE ITSELF !!! THIS IS THE CASE FOR ORC AND PARQUET AS WELL
- dont even need to mention about headers !!!

```

### 2) Creating df from Parquet file format :

```python
df = spark.read.parquet(r"file:///...")

df.show(3)


df = spark.read.load(r"")

here we are not providing any file type while creating the df, as parquet is the default file format in spark, we need to provide file path of only parquet file in .load() anyother files path will lead to error 

```


- avro file format stores data in Row-oriented format, therefore its good in std db things like , insert, update , deletes ,etc 


- for avro file schema can be present in the file itself or not.
- if not the data is in .avro file and the schema is in .avsc file !!

 
 ### 3) Creating df using avro file format :

- just read the schema and pass it while creating df from avro 

 ```python
schema_path = r"data/twitter.avsc"

with open(schema_path , "r") as f:
     avro_schema = f.read()


df = sprk.read.format("avro").option("avroschema",avro_schema).load(r"data\twitter.avro")

df.show()
    

 ```


--------------------------------------------------------------------------------------------


### Interview questions :

1) Multi-character delimiter :

- same delimiter throughout the file, but the delimiter in itself is of multi-characters :

```markdown
id|~|name|~|age|~|salary
1|~|John Doe|~|30|~|50000
2|~|Jane Smith|~|25|~|60000
3|~|Emily Johnson|~|45|~|70000

```

Q) Create a df from above :

```python
# Loading the data from a CSV file using the defined schema and a custom multicharacter delimiter
df = spark.read.format("csv") \
    .option("header", "true") \
    .option("delimiter", "|~|") \
    .schema(schema) \
    .load("data\multichar_delimited_data.txt")  # Adjust the path to your actual data file's location

need to use .csv() even though the file is .txt !!!

```

#### Here in above example we are using .csv() even though the file is .txt, so keep in mind the extension of the file does not matter , what method u are using defines how spark will try to parse the source file 

#### if u have .parquet file and try to use .csv() it will fail !! spark trusts u with the method u use not the extension of file 


----------------------------------------------------------------------------------------------


2) Multiple delimiters in a single file :

.txt file :
```markdown
id#name$age|salary
1#John Doe$30|50000
2#Jane Smith$25|60000
3#Emily Johnson$45|70000


need to break down columns and their rows using split()

split() function returns a list , usually in code define for columns, the rows will follow

.withColumn("col_name", value) allows us to add new columns to the new df being formed



```

```python 
from pyspark.sql.functions import *

df = spark.read.option("header","true").\
.option("sep","#").\
csv(r"file:///C:\Users\Sandeep\Desktop\data\mixed_demiliters.txt")

df.show()
id  name$age|salary
1  John Doe$30|50000         
2  Jane Smith$25|60000
3  Emily Johnson$45|70000


df1 = df.withColumn("name", split(col("name$age|salary"), "\$")[0])
id  name        name$age|salary
1  John Doe      John Doe$30|50000         
2  Jane Smith     Jane Smith$25|60000
3  Emily Johnson     Emily Johnson$45|70000




df2 = =df1.withColumn("age", split(split(col("name$age|salary"),"\$")[1], "\|")[0])
id  name        age   name$age|salary
1  John Doe      30    John Doe$30|50000         
2  Jane Smith    25    Jane Smith$25|60000
3  Emily Johnson 45    Emily Johnson$45|70000

df3 = =df2.withColumn("salary", split(split(col("name$age|salary"),"\$")[1], "\|")[1])
id  name        age   salary  name$age|salary
1  John Doe      30    50000   John Doe$30|50000         
2  Jane Smith    25    60000   Jane Smith$25|60000
3  Emily Johnson 45    70000   Emily Johnson$45|70000


df4 = df3.drop("name$age|salary")


## can wrap all of this in a single query as well 

df_final = df.withColumn("name", split(col("name$age|salary"), "\$")[0])\
             .withColumn("age", split(split(col("name$age|salary"),"\$")[1], "\|")[0])\
             .df2.withColumn("salary", split(split(col("name$age|salary"),"\$")[1], "\|")[1])\
             .drop("name$age|salary")


# we can split in any combo we want !! 









```