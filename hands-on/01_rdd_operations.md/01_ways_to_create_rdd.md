## Ways to create RDD: 
- dataset broken so that we can do distributed processing

### 1) sc.parallelize(data)  (from python variables)

```python
from pyspark import SparkContext

sc = SparkContext("local", "ParallelizeExample")   # (where to run , app_name) can create a session with lot of configs instead of this approach , using the .builder() and all
data = [1, 2, 3, 4, 5]
rdd = sc.parallelize(data)
rdd.collect()  ## shows the conetent present in RDD , not the partition wise data 
input("Press Enter to Exit : ")

data1 = {"a": 10, "b": 20, "c": 30}
rdd1 = sc.parallelize(data1.items())  if only data1 used then  results in only keys not k:v
rdd1.collect()  # [('a', 10), ('b', 20), ('c', 30)]


```

### 2) sc.textFile(r"")   (from a file)

```python
# You can create an RDD from an external dataset such as a local file system or HDFS using
# the textFile() method, which reads files as RDDs of text lines.

from pyspark import SparkContext

sc = SparkContext("local", "TextFileExample")
rdd = sc.textFile(r"file:///C:\Users\Sandeep\Desktop\data\wordcount.txt")
print(rdd.collect())

```

### 3) creating an RDD after applying transformation to some old RDD :

```python
# Every RDD transformation results in a new RDD. This means you can create a new RDD by
# applying transformations like map, filter, etc., to an existing RDD.

from pyspark import SparkContext

sc = SparkContext("local", "TransformationExample")
data = [1, 2, 3, 4, 5]
original_rdd = sc.parallelize(data)
transformed_rdd = original_rdd.map(lambda x: x * x)
print(transformed_rdd.collect())

```

### 4) creating_rdd_from_textfile_using_hadoopFile


```python
from pyspark import SparkContext

sc = SparkContext("local", "TextFileExample")

# Read a text file using TextInputFormat
rdd = sc.hadoopFile("data/movies.csv",
                    "org.apache.hadoop.mapred.TextInputFormat",
                    "org.apache.hadoop.io.LongWritable",
                    "org.apache.hadoop.io.Text")

print(rdd.collect())

```


### 5) creating_rdd_from_sequencefile_using_hadoopFile :

```python 

from pyspark import SparkContext

sc = SparkContext("local", "SequenceFileExample")

# Read a sequence file
rdd = sc.hadoopFile("data/movies_sequence",
                    "org.apache.hadoop.mapred.SequenceFileInputFormat",
                    "org.apache.hadoop.io.Text",
                    "org.apache.hadoop.io.IntWritable")

# Convert RDD to display as tuples
# result = rdd.map(lambda x: (x[0].toString(), int(x[1].get())))

print(rdd.collect())

```