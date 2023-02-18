# aws_lambda_function_Bigdata-project2-AWS-ETL-pipeline.

# About The Project:-
   I) FETCH DATA FROM API AND STORING DATA IN  S3 BUCKET USING AWS LAMBDA.
   
   II) READ DATA FROM S3 BUCKET USING AWS GLUE AND STORE FROM_DATE ,TO_DATE IN MONGO DB
   
   III) READ DATA FROM DYNAMO DB USING AWS GLUE
   
   IV) CHECK HOW MANY RECORD AVAILABLE FROM S3 BUCKET AVAILABLE IN DYNAMO DB.
   
   V) STORE UPCOMING NEW RECORDS ONLY IN DYNAMO DB.
   
   VI) ARCHIEVE OLD DATA OF S3



### AWS-ETL-PROJECT- ARCHITECTURE
<img width="552" alt="image" src="https://user-images.githubusercontent.com/71961635/219772494-3390ebd5-f87b-4df4-989a-164d136c90c0.png">

### Built With

This section should list any major frameworks/libraries used to bootstrap your project. Leave any add-ons/plugins for the acknowledgements section. Here are a few examples.

* AWS
* PYSPARK 
#  *LETS START BUILDING PROJECT*

## Input Configuration For Web Api

  WEB API ---->FROM_DATE TO TO_DATE WE HAVE TO PASS IN WEB API URL TO FETCH JSON DATTA----> S3 BUCKET

## FIRST CREATE A CONDA  ENVIRONMENT USING CONDA IN VISUAL STUDIO TERMINAL.
```create -p venv python==3.9.12 -y
```
![image](https://user-images.githubusercontent.com/71961635/219667921-2403d454-c22b-4e3b-bb94-4bf73e22cc74.png)

## THEN ACTIVATE CONDA ENVIRONMENT
```
conda activate venv/
```
![image](https://user-images.githubusercontent.com/71961635/219668510-69d835e1-4362-4f5a-ad4c-d534009a47c8.png)

## THEN INSTALL reuiresment.txt
```pip install -r requirements.txt
```
![image](https://user-images.githubusercontent.com/71961635/219668856-c9ae0ce2-c283-41a8-a98a-fd9a582dd3a5.png)

## Create a lambda_function_code folder and inside that folder lambda_function.py
code of lambda_function.py
```
import json
import pymongo
import certifi
import logging
import os
import boto3
import datetime
import os
import requests

ca = certifi.where()
import os
DATABASE_NAME = os.getenv("DATABASE_NAME")
COLLECTION_NAME = os.getenv("COLLECTION_NAME")
MONGODB_URL = os.getenv("MONGODB_URL")
BUCKET_NAME=os.getenv("BUCKET_NAME")

DATA_SOURCE_URL = f"https://www.consumerfinance.gov/data-research/consumer-complaints/search/api/v1/" \
                  f"?date_received_max=<todate>&date_received_min=<fromdate>" \
                  f"&field=all&format=json"
client = pymongo.MongoClient(MONGODB_URL, tlsCAFile=ca)

def get_from_date_to_date():
    from_date = "2023-01-01"
    from_date = datetime.datetime.strptime(from_date, "%Y-%m-%d")

    if COLLECTION_NAME in client[DATABASE_NAME].list_collection_names():

        res = client[DATABASE_NAME][COLLECTION_NAME].find_one(sort=[("to_date", pymongo.DESCENDING)])
        if res is not None:
            from_date = res["to_date"]

    to_date = datetime.datetime.now() #current date

    response = {
        "form_date": from_date.strftime("%Y-%m-%d"),
        "to_date": to_date.strftime("%Y-%m-%d"),
        "from_date_obj": from_date,
        "to_date_obj": to_date
    }
    logging.info(f"From date and to date {response}")
    return response

def save_from_date_to_date(data, status=True):
    data.update({"status": status})
    logging.info(f"saving from data and to date {data}")
    client[DATABASE_NAME][COLLECTION_NAME].insert_one(data)

def lambda_handler(event, context):
    print(event,context)
    from_date, to_date, from_date_obj, to_date_obj = get_from_date_to_date().values()
    if to_date==from_date:
        return {
            'statusCode': 200,
            'body': json.dumps('Pipeline has already downloaded all data upto yesterday')
        }
    url = DATA_SOURCE_URL.replace("<todate>", to_date).replace("<fromdate>", from_date)
    data = requests.get(url, params={'User-agent': f'your bot '})

    finance_complaint_data = list(map(lambda x: x["_source"],
                                    filter(lambda x: "_source" in x.keys(),
                                            json.loads(data.content)))
                                )
    s3 = boto3.resource('s3')
    s3object = s3.Object(BUCKET_NAME, f"inbox/{from_date.replace('-','_')}_{to_date.replace('-','_')}_finance_complaint.json")
    s3object.put(
        Body=(bytes(json.dumps(finance_complaint_data).encode('UTF-8')))
    )

    save_from_date_to_date({"from_date": from_date_obj, "to_date": to_date_obj})
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }

```

## THEN CONVERT YOUR CODE TO ZIP FILE TO UPLOAD IN LAMBDA USING BELOW

```
pip install --platform manylinux2014_x86_64 --target=<folder_name> --implementation cp --python 3.9 --only-binary=:all: --upgrade <lib1> <lib2>
```
EXAMPLE
```
pip install --platform manylinux2014_x86_64 --target=lambda_function_code --implementation cp --python==3.9.12 --only-binary=:all: --upgrade pymongo[srv] boto3 requests
```

Collecting pymongo[srv]
![image](https://user-images.githubusercontent.com/71961635/219670347-8a75d261-2a07-4062-8a73-8e579430688b.png)

## CREATE A MONGODB ATLAS CLUSTER AND CONNECT TO  MONGODB COMPASS USING URL.
![image](https://user-images.githubusercontent.com/71961635/219787052-1e778f89-b15f-48fe-9950-542fdd3afa8e.png)

##  MONGODB URL
![image](https://user-images.githubusercontent.com/71961635/219787355-e29dd0fd-3781-47e2-97e5-9b2024415da6.png)
![image](https://user-images.githubusercontent.com/71961635/219788372-1294040d-22bd-4c0e-a7e0-b9afe5d81c65.png)
## USING URL WE HAVE TO CONNECT MONGODB COMPASS
![image](https://user-images.githubusercontent.com/71961635/219832395-9f844d93-5c10-4a1f-9690-d0430c513fda.png)

## CREATE A LAMBDA FUNCION WITH  DownloadData NAME
![image](https://user-images.githubusercontent.com/71961635/219828986-6de9fba9-eba3-4531-b2fa-edf4fe6924e6.png)
---->GO TO BASIC SETTINGS  AND FOLOW BELOW STEPS
![image](https://user-images.githubusercontent.com/71961635/219829316-ca35e274-56a3-4d40-9147-af420fd7478e.png)
![image](https://user-images.githubusercontent.com/71961635/219829324-ca0995ff-f7c7-43bb-adf4-906ee617f78d.png)


---->UPLOAD ZIP IN DownloadData 

![image](https://user-images.githubusercontent.com/71961635/219794395-45667a82-f3fb-4a5c-a973-19e4dfdedc60.png)

-------> Permissions policies OR ROLE
![image](https://user-images.githubusercontent.com/71961635/219829416-b69302d0-c68c-4339-8679-c8499166a5cf.png)


## CREATE S3 BUCKET 
![image](https://user-images.githubusercontent.com/71961635/219829175-0ebbebb3-255a-4cc5-b738-91416bb3f008.png)

 --->EDIT ENVIRONMENTAL VARIABLES OF LAMBDA FUNCTION
 ![image](https://user-images.githubusercontent.com/71961635/219829108-446fb1c0-11d6-4359-a237-b10f91138f43.png)
 ---> NOW YOU CAN TEST YOUR  LAMBDA FUNCTION & CHECK S3 BUCKET  to check IS THERE ANY DATA
 
 ## THEN ADD TRIGGER  TO LAMBDA FUNCTION TO SCHEDULE JOB PERIODICALLY
 ![image](https://user-images.githubusercontent.com/71961635/219829620-4a433653-9158-4fe6-8442-ff9627006b27.png)

 # CAREFULLY WE HAVE TO PERFORM SOME STEPS:--
   I) READ DATA FROM S3 BUCKET USING AWS GLUE
   II)READ DATA FROM DYNAMO DB USING AWS GLUE
   III)CHECK HOW MANY RECORD AVAILABLE FROM S3 BUCKET AVAILABLE IN DYNAMO DB 
   III)STORE UPCOMING NEW RECORDS  IN DYNAMO DB.
   IV)ARCHIEVE DATA TO OLD DATA OF S3
   
 ## Provisioning Aws Dynamodb
  ![dynamo db](https://user-images.githubusercontent.com/71961635/219830089-67262afa-2beb-49c6-a90d-5daf0074513b.png)

  ![image](https://user-images.githubusercontent.com/71961635/219830058-fc0c5d65-24a8-4b7c-a9b7-5deacf6b3c94.png)
 
 ## CREATE AWS GLUE WITH PYSPARK SCRIPT
 ![gluejob](https://user-images.githubusercontent.com/71961635/219830255-d22c81ae-1a5f-4438-bd34-6bc79cdc6964.png)
 ![gluejob2](https://user-images.githubusercontent.com/71961635/219830265-9216e9ed-1944-44f9-89a4-269b01543d37.png)

```
  import sys
from awsglue.transforms import *
from awsglue.utils import getResolvedOptions
from pyspark.context import SparkContext
from pyspark.sql import functions as func
from awsglue.context import GlueContext
from awsglue.dynamicframe import DynamicFrame
from pyspark.sql.types import LongType
from awsglue.job import Job
import os 
## @params: [JOB_NAME]
args = getResolvedOptions(sys.argv, ['JOB_NAME'])

sc = SparkContext()
glueContext = GlueContext(sc)
spark = glueContext.spark_session
job = Job(glueContext)
job.init(args['JOB_NAME'], args)

#declaring constant variables
BUCKET_NAME="data327"
DYNAMODB_TABLE_NAME="fc_data"
INPUT_FILE_PATH=f"s3://{BUCKET_NAME}/inbox/*json"

#getting logger object to log the progress
logger  = glueContext.get_logger()
logger.info(f"Started reading json file from {INPUT_FILE_PATH}")
df_sparkdf=spark.read.json(INPUT_FILE_PATH)
logger.info(f"Type casting columns of spark dataframe to Long type")
df_sparkdf = df_sparkdf.withColumn("complaint_id",func.col("complaint_id").cast(LongType()))

logger.info(f"Columns in dataframe : {len(df_sparkdf.columns)}--> {df_sparkdf.columns}")
logger.info(f"Number of rows found in file: {df_sparkdf.count()} ")

dyf = glueContext.create_dynamic_frame.from_options(
    connection_type="dynamodb",
    connection_options={"dynamodb.input.tableName": DYNAMODB_TABLE_NAME,
        "dynamodb.throughput.read.percent": "1.0",
        "dynamodb.splits": "100"
    }
)
dyf_sparkdf=dyf.toDF()
new_sparkdf=None
if dyf_sparkdf.count()!=0:
    logger.info(f"Columns in dynamodb dataframe : {len(dyf_sparkdf.columns)}--> {dyf_sparkdf.columns}")
    logger.info(f"Number of rows found in file: {dyf_sparkdf.count()} ")
    logger.info(f"Renaming exiting complaint id column of dynamodb ")
    existing_complaint_spark_df = dyf_sparkdf.select("complaint_id").withColumnRenamed("complaint_id","existing_complaint_id")
    logger.info(f"Applying left join on new dataframe from s3 and dynamo db ")
    joined_sparkdf = df_sparkdf.join(existing_complaint_spark_df,df_sparkdf.complaint_id==existing_complaint_spark_df.existing_complaint_id,"left")
    logger.info(f"Number of row after left join : {joined_sparkdf.count()}")
    new_sparkdf = joined_sparkdf.filter("existing_complaint_id is null")
    new_sparkdf.drop("existing_complaint_id")
    new_sparkdf=new_sparkdf.coalesce(10)
else:
    new_sparkdf=df_sparkdf.coalesce(10)

logger.info(f"Converting spark dataframe to DynamicFrame")
newDynamicFrame= DynamicFrame.fromDF(new_sparkdf, glueContext, "new_sparkdf")
logger.info(f"Started writing new records into dynamo db dataframe.")
logger.info(f"Number of records will be written to dynamodb: {new_sparkdf.count()}")
glueContext.write_dynamic_frame_from_options(
    frame=newDynamicFrame,
    connection_type="dynamodb",
    connection_options={"dynamodb.output.tableName": DYNAMODB_TABLE_NAME,
        "dynamodb.throughput.write.percent": "1.0"
    }
)

logger.info(f"Data has been dumped into dynamodb ")
logger.info(f"Archiving file from inbox source: s3://{BUCKET_NAME}/inbox  to archive: s3://{BUCKET_NAME}/archive ")
os.system(f"aws s3 sync s3://{BUCKET_NAME}/inbox s3://{BUCKET_NAME}/archive")

logger.info(f"File is successfully archived.")
os.system(f"aws s3 rm s3://{BUCKET_NAME}/inbox/ --recursive")
    
job.commit()

```
## CREATE A Schedule
![image](https://user-images.githubusercontent.com/71961635/219830360-3dc826ae-2792-464c-a88e-fb84c8a7cc3b.png)

