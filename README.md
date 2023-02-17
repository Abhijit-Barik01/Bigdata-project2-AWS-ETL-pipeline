# aws_lambda_function_Bigdata-project2-AWS-ETL-pipeline.

### AWS-ETL-PROJECT- ARCHITECTURE

<img width="552" alt="image" src="https://user-images.githubusercontent.com/71961635/219772494-3390ebd5-f87b-4df4-989a-164d136c90c0.png">


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

## CREATE A MONGODB ATLAS CLUSTER AND CONNECT TO  MONGODB USING URL.
![image](https://user-images.githubusercontent.com/71961635/219787052-1e778f89-b15f-48fe-9950-542fdd3afa8e.png)

## CONNECT TO MONGODB COMPASS WE NEED URL
![image](https://user-images.githubusercontent.com/71961635/219787355-e29dd0fd-3781-47e2-97e5-9b2024415da6.png)
![image](https://user-images.githubusercontent.com/71961635/219788372-1294040d-22bd-4c0e-a7e0-b9afe5d81c65.png)
## USING URL WE HAVE TO CONNECT MONGODB COMPASS
![image](https://user-images.githubusercontent.com/71961635/219789144-214e5472-23ab-4ae6-be05-b7de3f39fe30.png)

## CREATE A LAMBDA FUNCION WITH  DownloadData NAME
---->UPLOAD ZIP IN DownloadData 

![image](https://user-images.githubusercontent.com/71961635/219794395-45667a82-f3fb-4a5c-a973-19e4dfdedc60.png)

