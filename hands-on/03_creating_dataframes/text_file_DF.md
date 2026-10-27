## Creating dataframes using different types of files :

### 1) csv file and using inferschema :

#### **if u have very huge files (60 million records) u should not use inferSchema as true becasue spark scans the entire file first and then only the schema is decided, so everytime u wanna create a dataframe and u use inferschema on such big source files then its time consuming and resource wasting**

### **in this case spark scans the entire value for each column and if different datatypes are present , it tries to assign such a datatype which can accomodate all the datatypes , the order is :**

### **null → Integer → Long → Decimal → Double → Timestamp/Date → Boolean → String**

```python
# Create a DataFrame from employee.txt (csv) file by implicitly providing Schema
# Importing the necessary libraries
from pyspark.sql import SparkSession

# Creating a Spark session
spark = SparkSession.builder \
    .appName("Employee Data Analysis") \
    .getOrCreate()

# Read the employee data from a CSV file into a DataFrame
# Replace '<path to employee.csv>' with the actual path to your CSV file
employeeDF = spark.read.option("inferSchema", "true")\     
    .option("header", "true").csv(r"data/employee.txt")   # \ means part of the same line , just shown on next line    , relative path to where the current script file is present is given for the csv file 

# Print the inferred schema of the DataFrame to show the data types and structure
employeeDF.printSchema()

col_name : datatype ,etc are set by the spark

# Display the contents of the DataFrame to provide a snapshot of the data
employeeDF.show()

# displays top 3 rows of the df
employeeDF.show(3)

# Stop the Spark session
spark.stop()


```




### Q) if we dont provide schema explicitly and also inferSchema is set to false (if we dont provide inferschema then also its its set to false by default ) then what happens when we try to create dataframe will it form ??
**Ans : yes , the dataframe will be formed but the DataType of all the columns will be string**


-------------------------------------------------------------------------------------------------


### 2) csv file and explicitly defining schema from our-side :

```python
# Create a DatFrame from employee.txt (csv) by providing schema explicitly

# Read Modes :
# PERMISSIVE(Default): Whenever schema mismatch happens : insert NULL for mismatched values
# FAILFAST : Whenever schema mismatch happens : ERROR will occur
# DROPMALFORMED : Whenever schema mismatch happens : DROP mismatched Records

# Importing necessary libraries for Spark and data types
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, IntegerType, StringType, DoubleType

# Creating a Spark session
spark = SparkSession.builder \
    .appName("Employee Data with Custom Schema") \
    .getOrCreate()

# Define a custom schema for the employee DataFrame
emp_schema = StructType([
    StructField("eid", IntegerType(), True),
    StructField("ename", StringType(), True),
    StructField("dept", StringType(), True),
    StructField("salary", DoubleType(), True),
    StructField("date_of_joining", StringType(), True)
])

#Alternate Way to create schema
emp_schema_2 = StructType().\
    add("eid", IntegerType(), True).\
    add("emp_name", StringType(), True).\
    add("dept", StringType(), True).\
    add("salary", DoubleType(), True).\
    add("date_of_joining", StringType(), True)


# Read the employee data from a CSV file into a DataFrame using the defined schema
empDF = spark.read.option("header", "true").schema(emp_schema_2).csv("data/employee.txt")

# Print the schema of the DataFrame to show the data types and structure
empDF.printSchema()

# Display the contents of the DataFrame to provide an overview of the data
empDF.show()

# Stop the Spark session
spark.stop()

```

- **Nullable is always true for all the columns when using inferschema and even if we enforce in schema, its relaxed back not properly enforced**


--------------------------------------------------------------------------------------------------


### 3) Creating DF from RDD :

- first create a rdd from a file and then clean it and then form the dataframe out of it
- Nobody uses this , just for curosity

```python
# Create RDD from employee.txt (csv) file, later Create DataFrame using  previously created RDD.

# Importing necessary libraries for Spark
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, IntegerType, StringType, DoubleType

# Creating a Spark session
spark = SparkSession.builder \
    .appName("Employee Data Processing with RDDs") \
    .getOrCreate()

# Access SparkContext from SparkSession for RDD creation
sc = spark.sparkContext

# Read the text file containing employee data into an RDD
# Replace '<path to employee.txt>' with the actual path to your text file
empRDD = sc.textFile("data/employee.txt")

# Display the first three lines of the RDD to inspect the structure
initial_lines = empRDD.take(3)
print("Initial lines:", initial_lines)

# Extract the header from the RDD to separate it from the data
emp_header = empRDD.first()

# Filter out the header line from the RDD to isolate the data rows
empRDD1 = empRDD.filter(lambda x: x != emp_header)

# Display the first three lines of the filtered RDD to verify header removal
filtered_lines = empRDD1.take(3)
print("Filtered lines:", filtered_lines)

# Split each data row by comma to create lists of individual elements
empRDD2 = empRDD1.map(lambda x: x.split(','))

# Display the first three lines of the RDD after splitting
split_lines = empRDD2.take(3)
print("Split lines:", split_lines)

# Convert each element in the RDD to the appropriate data type and create tuples
empRDD3 = empRDD2.map(lambda x: (int(x[0]), x[1], x[2], float(x[3]), x[4]))

# Display the first three lines of the RDD after type conversion
typed_lines = empRDD3.take(3)
print("Typed lines:", typed_lines)

# Convert the RDD of tuples to a DataFrame
empDF = spark.createDataFrame(empRDD3, schema=["eid", "ename", "dept", "salary", "date_of_joining"])

# Show schema of empDF
empDF.printSchema()

# Show the DataFrame with its contents
empDF.show()

# Stop the Spark session
spark.stop()

```


----------------------------------------------------------------------------------------------

### 4) creating DF from rdd by explicitly defining schema :

```python 
# Create RDD from employee.txt (csv) file, later Create DataFrame using previously
# created RDD by explicitly defining schema.

# Importing necessary libraries for Spark, Spark SQL, and data types
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, IntegerType, StringType, DoubleType

# Creating a Spark session
spark = SparkSession.builder \
    .appName("Employee Data Processing with RDDs") \
    .getOrCreate()

# Access SparkContext from SparkSession for RDD creation
sc = spark.sparkContext

# Read the text file containing employee data into an RDD
# Replace '<path to employee.txt>' with the actual path to your text file
empRDD = sc.textFile("file:///<path to employee.txt>")

# Display the first three lines of the RDD to inspect the structure
print("Initial lines:", empRDD.take(3))

# Extract the header from the RDD to separate it from the data
emp_header = empRDD.first()

# Filter out the header line from the RDD to isolate the data rows
empRDD1 = empRDD.filter(lambda x: x != emp_header)

# Display the first three lines of the filtered RDD to verify header removal
print("Filtered lines:", empRDD1.take(3))

# Split each data row by comma to create lists of individual elements
empRDD2 = empRDD1.map(lambda x: x.split(','))

# Display the first three lines of the RDD after splitting
print("Split lines:", empRDD2.take(3))

# Convert each element in the RDD to the appropriate data type and create tuples
empRDD3 = empRDD2.map(lambda x: (int(x[0]), x[1], x[2], float(x[3]), x[4]))

# Display the first three lines of the RDD after type conversion
print("Typed lines:", empRDD3.take(3))

# Define the schema for the employee DataFrame, specifying each column's name, data type, and nullability
emp_schema = StructType([
    StructField("id", IntegerType(), True),
    StructField("name", StringType(), True),
    StructField("dept", StringType(), True),
    StructField("salary", DoubleType(), True),
    StructField("doj", StringType(), True)
])

# Create a DataFrame from the RDD of tuples using the defined schema
empDF = spark.createDataFrame(empRDD3, emp_schema)

# Display the schema of empDF
empDF.printSchema()

# Display the contents of the DataFrame
empDF.show()

# Stop the Spark session
spark.stop()

```


-------------------------------------------------------------------------------------------


### 5) Creating DF from a custom delimiter :

- like instead of , seperated if spererator is |  or # then ??

```python
df = spark.read \
    .option("header", "true") \
    .option("delimiter", "|") \
    .schema(schema) \
    .csv("data/users_pipe_separated.txt")
```

- if the seperator/delimiter is tabs the use "/t"   



---------------------------------------------------------------------------------------------


### 6) Creating DF from a json file :

```python

# std import and creating a spark session  

# Read the movie data from a JSON file into a DataFrame
# Replace '<path to movies.json>' with the actual path to your JSON file
moviesDF = spark.read.option("inferSchema", "true").json("data/movies.json")

# Print the schema of the DataFrame to show the inferred data types and structure
moviesDF.printSchema()

# Display the contents of the DataFrame to provide insight into the movie data
moviesDF.show()
## proper table will be shown 

# Stop the Spark session
spark.stop()


```

**If a field is missing in the json file then NULL will be shown in the dataframe !! **


-----------------------------------------------------------------------------------------------

### 7) Creating DF from a NESTED json file 

```json
[
  {
    "id": 1,
    "name": "John",
    "address": {
      "city": "New York",
      "state": "NY"
    },
    "phone_numbers": [
      {
        "type": "home",
        "number": "123-456-7890"
      },
      {
        "type": "work",
        "number": "987-654-3210"
      }
    ]
  },
  {
    "id": 2,
    "name": "Jane",
    "address": {
      "city": "San Francisco",
      "state": "CA"
    },
    "phone_numbers": [
      {
        "type": "home",
        "number": "111-222-3333"
      },
      {
        "type": "work",
        "number": "444-555-6666"
      }
    ]
  }
]

```

code :

```python
# Reading a JSON file with nested data, the option "multiLine" set to True allows reading multi-line JSON files
df = spark.read.option("multiLine", "true").json(r"data/nested_json.json")


# Printing the schema of the DataFrame to understand the structure of the nested JSON data
df.printSchema()

address : struct type
phone no. : array type


# Displaying the contents of the DataFrame in a tabular format
df.show()

| address              | id | name | phone_numbers          |
|----------------------|----|------|------------------------|
| {New York, NY}       | 1  | John | [{123-456-7890, h...   |
| {San Francisco, CA}  | 2  | Jane | [{111-222-3333, h...   |

see nested wale k column names are not shown directly values !!!


# Stopping the SparkSession
spark.stop()
```


-------------------------------------------------------------------------------------------------


### 8) Extracting schema from a dataframe :

```python 

movie_schema = df.schema 


StructType([StructField('address', StructType([StructField('city', StringType(), True), StructField('state', StringType(), True)]), True), StructField('id', LongType(), True), StructField('name', StringType(), True), StructField('phone_numbers', ArrayType(StructType([StructField('number', StringType(), True), StructField('type', StringType(), True)]), True), True)])


```

#### Q) if u have 150 cols and u wanna create df will u be manually writing the schema by yourself and waste time ?
 Ans : No, create a sample file of 150 col , u trust this file as u have validated about data type and all, now just simply extract the schema from df.schema method and then use it to create actual dataframe !!


--------------------------------------------------------------------------------------------------


### 9) Creating DF from a XML file :

- need a seperate package to be installed 
- pyspark --packages com.databricks:spark-xml_2.12...

```xml
<people>
  <person>
    <id>1</id>
    <name>John</name>
    <city>New York</city>
  </person>
  <person>
    <id>2</id>
    <name>Jane</name>
    <city>San Francisco</city>
  </person>
</people>

person is the row level
```



```
df = spark.read.format("xml").option()...load(r"")
```




------------------------------------------------------------

## Interview questions pending : 

- multiple delimiter and mult character delimiter 

- json handling also remaining

