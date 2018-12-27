<!-- TITLE: Data Compression -->

*Data compression and compression formats can have a significant impact on performance.Three important places to consider data compression are in MapReduce and Spark jobs, data stored in HBase, and Impala queries. For the most part, the principles are similar for each.*

*You must balance the processing capacity required to compress and uncompress the data, the disk IO required to read and write the data, and the network bandwidth required to send the data across the network. The correct balance of these factors depends upon the characteristics of your cluster and your data, as well as your usage patterns.*

*Compression is not recommended if your data is already compressed (such as images in JPEG format). In fact, the resulting file can sometimes be larger than the original.*

# Compression Types
Hadoop supports the following compression types and codecs:

```
gzip - org.apache.hadoop.io.compress.GzipCodec
bzip2 - org.apache.hadoop.io.compress.BZip2Codec
LZO - com.hadoop.compression.lzo.LzopCodec
Snappy - org.apache.hadoop.io.compress.SnappyCodec
Deflate - org.apache.hadoop.io.compress.DeflateCodec
```

* GZIP compression uses more CPU resources than Snappy or LZO, but provides a higher compression ratio. GZip is often a good choice for cold data, which is accessed infrequently. Snappy or LZO are a better choice for hot data, which is accessed frequently.
* BZip2 can also produce more compression than GZip for some types of files, at the cost of some speed when compressing and decompressing. HBase does not support BZip2 compression.
* Snappy often performs better than LZO. It is worth running tests to see if you detect a significant difference.
* For MapReduce, if you need your compressed data to be splittable, BZip2 and LZO formats can be split. Snappy and GZip blocks are not splittable, but files with Snappy blocks inside a container file format such as SequenceFile or Avro can be split. Snappy is intended to be used with a container format, like SequenceFiles or Avro data files, rather than being used directly on plain text, for example, since the latter is not splittable and cannot be processed in parallel using MapReduce. Splittability is not relevant to HBase data.
* For MapReduce, you can compress either the intermediate data, the output, or both. Adjust the parameters you provide for the MapReduce job accordingly. The following examples compress both the intermediate data and the output. MR2 is shown first, followed by MR1.

# MRv2
```
mapreduce.compress.map.output=true
mapreduce.map.output.compression.codec=org.apache.hadoop.io.compress.SnappyCodec
mapreduce.output.compress=true
mapreduce.output.compression.codec=org.apache.hadoop.io.compress.SnappyCodec
```
#MRv1
```
mapred.compress.map.output=true
mapred.map.output.compression.codec=org.apache.hadoop.io.compress.GzipCodec
mapred.output.compress=true
mapred.output.compression.codec=org.apache.hadoop.io.compress.GzipCodec
```




