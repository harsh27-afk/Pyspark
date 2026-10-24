## Group by key and Reduce by key :


### K:V pair RDD 

```markdown
- there are normal rdd like a list and then there are k:v rdd which are in pairs

data = [1,2,3]

rdd = sc.parallelize(data)

kvRDD = rdd.map(lambda x: (x,"demo"))

kvRDD.collect()
o/p : [(1,"demo"), (2,"demo"), (3,"demo")]


```

- so GBK and RBK can be applied only on K:V pair RDDs

