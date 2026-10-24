## stuff u need :

1) python
2) java jdk
3) pyspark   ( need to install these 3 and add env path of pc and all)

- in project create venv and use the dependencies for isolation and avoiding global pollution

- i think we need : pip install pyspark (not sure though , if we installed pyspark s/w we dont need : pip install pyspark ig )


- For practicing u can use : 
1) ide with git (used in prod)
2) databricks notebook
3) pyspark shell (best for practicing)



- **u can even run the entire pyspark code scripts locally on personal pc, without using cluster manager and all it will be time consuming though!!!**

- running code in spark shell is faster because it loads jvm process once only and keeps it alive, incase of ide based script running the jvm process is killed and started again for every script run 

- When you set .master("local[*]"), the driver and executor all run in the same JVM process on your machine.  Spark uses multiple threads (one per core) to parallelize tasks. You get a full Spark UI at http://localhost:4040 where you can inspect DAGs, stages, and tasks.

```python 
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("Practice") \
    .master("local[*]") \   ---> imp line
    .getOrCreate()   

```

- the data for execrices is present in /data folder in each sub-folder , **while running in spark shell need to provide absoulte path of data file present in /data folders**

- also need to create temp views of df when dealing with spark sql

