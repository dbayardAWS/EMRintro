# EMRintro
Intro to EMR
dave

Spin up Cluster
## Connect via Event Engine
## Create a Cloud9 Environment - https://github.com/dbayardAWS/Redshift-labs/blob/master/Cloud9Redshift.md
## Create a Key Pair
## 1c Launch EMR
## 1c Setup Security Group
## 1c Connect via SSH

Hive and Pig
## Create S3 bucket and populate it
## Hive- create an external table and query it via Hive CLI
## Hive- create another external table and insert into it via EMR Steps
## Pig- convert data into Avro format via EMR Steps

Spark
## Spark- run a PySpark job via spark-submit CLI
## Spark- run a PySpark job via EMR Steps
## Logs- View the YARN Application Info
## Logs- View the SparkUI/Spark History Info

EMR Notebook
## Launch EMR Notebook
## Connect to EMR Notebook and open a new JupyterLab notebook
## Run PySpark in the Notebook




cat <<\EOF00 > ny-taxi.pig
DEFINE CSVLoader org.apache.pig.piggybank.storage.CSVLoader();

NY_TAXI = LOAD '$INPUT' USING CSVLoader(',') AS
  (vendor_id:int,
  lpep_pickup_datetime:chararray,
  lpep_dropoff_datetime:chararray,
  store_and_fwd_flag:chararray,
  rate_code_id:int,
  pu_location_id:int,
  do_location_id:int,
  passenger_count:int,
  trip_distance:double,
  fare_amount:double,
  mta_tax:double,
  tip_amount:double,
  tolls_amount:double,
  ehail_fee:double,
  improvement_surcharge:double,
  total_amount:double,
  payment_type:int,
  trip_type:int);
STORE NY_TAXI into '$OUTPUT' USING org.apache.pig.piggybank.storage.avro.AvroStorage();
EOF00

aws s3 cp ny-taxi.pig s3://$BUCKET/files/
aws s3 ls s3://$BUCKET/files/

go back to instructions...


aws s3 ls s3://$BUCKET/output/pig/
aws s3 cp s3://$BUCKET/output/pig/part-v000-o000-r-00000.avro /tmp/pigout.txt
head -5 /tmp/pigout.txt | cat -v


--------------
cat <<\EOF00 > spark-etl.py
import sys
from datetime import datetime

from pyspark.sql import SparkSession
from pyspark.sql.functions import *

if __name__ == "__main__":

    print(len(sys.argv))
    if (len(sys.argv) != 3):
        print("Usage: spark-etl [input-folder] [output-folder]")
        sys.exit(0)

    spark = SparkSession\
        .builder\
        .appName("SparkETL")\
        .getOrCreate()

    nyTaxi = spark.read.option("inferSchema", "true").option("header", "true").csv(sys.argv[1])

    updatedNYTaxi = nyTaxi.withColumn("current_date", lit(datetime.now()))

    updatedNYTaxi.printSchema()

    print(updatedNYTaxi.show())

    print("Total number of records: " + str(updatedNYTaxi.count()))
    
    updatedNYTaxi.write.parquet(sys.argv[2])
EOF00

scp -i key.pem spark-etl.py hadoop@ec2-3-88-129-248.compute-1.amazonaws.com:spark-etl.py
echo RUN THIS COMMAND IN YOUR SSH TERMINAL TO EMR:
echo spark-submit spark-etl.py s3://$BUCKET/input/ s3://$BUCKET/output/spark


go back to instructions...


aws s3 ls s3://$BUCKET/output/spark/

--hint: use s3 console UI to view this file and select from it


--- launch EMR notebook
--- pyspark

## creates a ny-taxi.table file
cat <<EOF00 > jupyter.py
import sys
from datetime import datetime
from pyspark.sql.functions import *

nyTaxi = spark.read.option("inferSchema", "true").option("header", "true").csv("s3://$BUCKET/input/")
updatedNYTaxi = nyTaxi.withColumn("current_date", lit(datetime.now()))
updatedNYTaxi.printSchema()
print(updatedNYTaxi.show())
print("Total number of records: " + str(updatedNYTaxi.count()))
EOF00


