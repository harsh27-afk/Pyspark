### 1) Parquet file format :


- Parquet is a columnar file format for storing tabular data and semi - structured kind of data

- Parquet = a collection of records that all follow the same schema, where each field can be simple or nested.

- Row oriented file formats store data row wise 

- the enxtension is .parquet  : file_name.parquet

- default file format in pyspark is parquet

- Parquet is useful only in big data , if u have small data then use csv instead !!!

**dont watch in preview mode for following part**

suppose table is :
id	name	country	amount
1	Asha	IN	250
2	Bob	US	100
3	Chen	CN	300

- row oriented formats like csv, json , avro store internally like :
1, Asha, IN, 250 | 2, Bob, US, 100 | 3, Chen, CN, 300

- but the column oriented formats like Parquet and ORC store like :
1, 2, 3 | Asha, Bob, Chen | IN, US, CN | 250, 100, 300

- Columnar storage allows helps in :
  - compressing the data ( like intenall stuff happens like maybe indexing and all that shit, too many same values so lets compress it kind of thing) is easier as each column will individually have same type of data and similar data values to some extent 

  - Fast **Analytical Queries** as the compute engine would require less data to scan , it will only scan the values of the columns u ask , so unlike row oriented storages where compute engine has to scan the unneccessary column values from the same row !! it does not happen anymore using columnar stored data.

  - Poor at fetching full records, even lets say u aggregated and all and now u need full row level info it sucks, but not that much i guess , cause the engine will shortlist the data first (by grouping and all) and then try to SELECT * so anyways it would not need caatastrophic time ig

- Parquet is best for high performance and space saving used in big data

### Compression :

- Parquet also compresses the data , there fore saves a lot of space , therefore u see file sizes are small

- same big data in csv and in parquet, the size of parquet is ~3x smaller 


### where is the parquet format used ?

- big data analytics, apache ecosystem , ml, etl pipelines, cloud storage


### size vs speed :

- json has higest size and lowest speed     

- parquet is less in size but not as speedy as avro 

- avro file takes more space then parquet but is faster than parquet



-----------------------------------------------------------------------------------------------------


- u cant edit parquet file in-place !! (i.e in the same file u cant change values) to make changes u have to create new parquet file 

- In industry 1 table can have many parquet files clubbed under a folder, so that these files can be distributed to multiple worker nodes 








