### glom()

```python

rdd = sc.parallelize([1,2,3,4,5,6], 3)  # the 3 means 3 partitions will be made in the rdd

newRdd = rdd.glom()      # new rdd
                         # glom() turns each partition into a list
newRdd.collect()         # output of .collect() will always be a list 
                         
standalone .collect() :  wraps all the elements present in rdd in a list and prints it 

"""
parallelize(data, 3) creates an RDD with 3 partitions.
glom() combines the elements of each partition into a list.
collect() brings the results to the driver and displays them as a Python list.
"""

```

------------------------------------------------------------------------------------------

### map()

- applies the defined logic on each and every element present in the rdd

```python
rdd = [1,2,3,4,5]
squareRDD = rdd.map(lambda x: x*x)    # uses implicit returns 
squareRDD.collect() 

o/p : [1,4,8,16,25]

# u can even define normal function and pass them in .map()

```

#### use-cases of map :
1) simple element wise transformation 
2) data cleaning ( eg: lowercasing , trimming , formatting )
3) math computation on nummeric data 

--------------------------------------------------------------------------------------------

### mapPartitions() :

```python

rdd6 = sc.parallelize([1, 2, 3, 4, 5, 6], 3)
print("MapPartitions Ex1:", rdd6.mapPartitions(lambda it: [sum(it)]).collect())

[3,7,11]

it - iterable

# mapPartitions() applies on each partition of the rdd, where as map() was being applied on every element of the rdd

# as the entire partition is being sent as an iterable obj more ram will be need in mapPartitions()
# the no. of function call is reduced though using mapPartitions()

```

#### worry about the size of each element in rdd , not the no. of elements 

----------------------------------------------------------------------------------------------------


### mapPartitionWithIndex() :

indexing as well of the partitions is available !!

```python

# Tag each element with its partition index
rdd1 = sc.parallelize(["apple", "banana", "cherry", "date"], 2)
result1 = rdd1.mapPartitionsWithIndex(lambda idx, it: [(idx, x) for x in it]).collect()

```

----------------------------------------------------------------------------------------------------


### mapValues() :

- works only on key:value pair RDD 
- works on values only does not even touch the keys

```python
rdd11 = sc.parallelize([("a", 1), ("b", 2)])
print("MapValues Ex1:", rdd11.mapValues(lambda v: v * 2).collect())

output : [("a",2),("b",4)]


```


----------------------------------------------------------------------------------------------

### map()  vs  flatMap() :

```python
rdd = ["apache", "spark", "hadoop"]

mapRDD = rdd.map(lambda x: x.upper())

or

def c2Upper(word) :
    return word.upper()

mapRDD2 = rdd.map(c2Upper)  # here we are not calling the function the args will be sent by default from the rdd words

```


#### flatmap()

- similar to map , just flattens the output 

```python

  data = ["Hello world", "Welcome to PySpark"]
  rdd = sc.parallelize(data)
  map_rdd = rdd.map(lambda line: line.split(" "))
  map_rdd.collect()
  # Output: [['Hello', 'world'], ['Welcome', 'to', 'PySpark']]
  flat_mapped_rdd = rdd.flatMap(lambda line: line.split(" "))
  flat_mapped_rdd.collect()
  # Output: ['Hello', 'world', 'Welcome', 'to', 'PySpark']

  FlatMap()  : flattens to 1 level only , if input is deeply nested and u want to flatten to utmost level , then u will need recursive logic in the flatMap() of ur own !!!



```



### 5. `groupByKey`
- Description: Groups the values for each key in the RDD.
- Syntax: `rdd.groupByKey()`
- Example:

  data = [("a", 1), ("b", 2), ("a", 3)]
  rdd = sc.parallelize(data)
  grouped_rdd = rdd.groupByKey().mapValues(list)
  grouped_rdd.collect()
  # Output: [('a', [1, 3]), ('b', [2])]

- Practice Examples:
  1. Group by key and sum the values.
  2. Group by key and count the number of values for each key.



### 6. `reduceByKey`
- Description: Combines the values for each key using a specified function.
- Syntax: `rdd.reduceByKey(function)`
- Example:

  data = [("a", 1), ("b", 2), ("a", 3)]
  rdd = sc.parallelize(data)
  reduced_rdd = rdd.reduceByKey(lambda x, y: x + y)
  reduced_rdd.collect()
  # Output: [('a', 4), ('b', 2)]

- Practice Examples:
  1. Reduce by key and multiply the values.
  2. Reduce by key and find the maximum value for each key.



  # its pending do revisit : video of 7 hrs

  performance wise reduceBy key is better than group by key, as in group by key it brings all the info down to clusters to group by, where as in reduce by agg happens on those clusters only not too much data shuffling