# aws_lambda_functionBigdata-project2-AWS-ETL-pipeline.




<img width="552" alt="image" src="https://user-images.githubusercontent.com/71961635/219772494-3390ebd5-f87b-4df4-989a-164d136c90c0.png">





#FIRST CREATE A  ENVIRONMENT USING CONDA IN VISUAL STUDIO TERMINAL.
create -p venv python==3.9.12 -y
![image](https://user-images.githubusercontent.com/71961635/219667921-2403d454-c22b-4e3b-bb94-4bf73e22cc74.png)

#THEN ACTIVATE CONDA ENVIRONMENT
conda activate venv/
![image](https://user-images.githubusercontent.com/71961635/219668510-69d835e1-4362-4f5a-ad4c-d534009a47c8.png)

#THEN INSTALL reuiresment.txt
pip install -r requirements.txt
![image](https://user-images.githubusercontent.com/71961635/219668856-c9ae0ce2-c283-41a8-a98a-fd9a582dd3a5.png)


#THEN CONVER YOUR CODE TO LAMBA UPLOADED FILE USING  BELOW COMMAND
use below commadn

```
commandline
pip install --platform manylinux2014_x86_64 --target=<folder_name> --implementation cp --python 3.9 --only-binary=:all: --upgrade <lib1> <lib2>
```

```
pip install --platform manylinux2014_x86_64 --target=lambda_function_code --implementation cp --python==3.9.12 --only-binary=:all: --upgrade pymongo[srv] boto3 requests
```

Collecting pymongo[srv]
![image](https://user-images.githubusercontent.com/71961635/219670347-8a75d261-2a07-4062-8a73-8e579430688b.png)
