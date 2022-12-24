# Wine Quality Prediction over AWS EMR

This application is a wine quality prediction ML model in Spark over AWS. The model is trained in parallel using 4 EC2 instances. Then, it is saved
and loaded in a Spark application that will perform wine quality prediction. This application is executed on one EC2 instance. The application is implemented in Python on Ubuntu Linux. 

## Input for model training:
We use 2 datasets with you for your ML model:
o TrainingDataset.csv: This dataset is used to train the model in parallel on multiple EC2 instances. 
o ValidationDataset.csv: This dataset is used to validate the model and optimize its performance. 


## The purpose of this project is to learn how to develop parallel machine learning (ML) applications in Amazon AWS cloud platform.
Specifically, you will learn: 
- how to use Apache Spark to train an ML model in parallel on multiple EC2 instances
- how to use Spark’s MLlib to develop and use an ML model in the cloud
- How to use Docker to create a container for your ML model to simplify model deployment.


## Docker container:
A Docker container is built for this prediction application. In this way, the prediction model can be quickly deployed across many different environments.


##Here is a step by step guide for developing and deploying the application:

### Setting up the environment:
- On your AWS account, head over to `EMR`, and create a cluster, with 5 nodes, 1 of which will serve as the master node and the others as slave nodes.

- After the cluster is created, an S3 bucket for that cluster will be generated in `S3`. Upload your `Training.py` file, `requirements.txt` file and the datasets on the bucket in a folder say wine-quality-prediction.

- Now go to your terminal, and connect to master EC2 instance using SSH:
```
ssh -i ~/mykey.cer hadoop@ec2-3-95-196-42.compute-1.amazonaws.com
```

- Copy the stored files from S3 bucket to the master node by using the following commands in your EMR shell:

```
aws s3 cp s3://aws-logs-877244108283-us-east-1/elasticmapreduce/j-127WJ4N9UI3WA/wine-quality-prediction/ ./ --recursive
```

- `cd` into the folder containing all the necessary files

- Make these files accessible to the slave nodes:
`hadoop fs -put TrainingDataset.csv`
`hadoop fs -put ValidationDataset.csv`


- You may find all the files stored there by using `ls`. Install all the packages and requirements by using `pip install -r requirements.txt`

- Install Scala:
`wget https://downloads.lightbend.com/scala/2.12.4/scala-2.12.4.rpm`
`sudo yum install scala-2.12.4.rpm`


- Install Spark:
`wget https://dlcdn.apache.org/spark/spark-3.3.1/spark-3.3.1-bin-hadoop3.tgz`
`sudo tar xvf spark-3.3.1-bin-hadoop3.tgz -C /opt`
`sudo chown -R ec2-user:ec2-user /opt/spark-3.3.1-bin-hadoop3`
`sudo ln -fs spark-3.3.1-bin-hadoop3 /opt/spark`

- Set path for spark
`vim ~/.bash_profile`
`echo -e "export SPARK_HOME=/opt/spark \n PATH=$PATH:$SPARK_HOME/bin \n export PATH" >> ~/.bash_profile`
`source  ~/.bash_profile`


### Training:
- Run the Training.py using: 
`spark-submit Training.py`

- Copy the saved model from S3 bucket onto master node:
`mkdir model`
`aws s3 cp s3://aws-logs-877244108283-us-east-1/elasticmapreduce/j-127WJ4N9UI3WA/wine-qual/model/ ./model --recursive`


### Testing:
- Run the Testing.py file: - 
`python Test.py`


### Containerization:
- Install Docker:
```
sudo yum search docker
sudo yum info docker
sudo yum install docker
sudo usermod -a -G docker ec2-user
sudo id ec2-user
sudo newgrp docker
sudo systemctl enable docker.service
sudo systemctl start docker.service
```

- Pull the docker image from your dockerhub repo:
`docker pull ks468/winepredict:latest`

- Run the docker image using this command:
docker run --volume /home/hadoop/:/home/myuser/ ks468/winepredict

### Link to docker repo:
https://hub.docker.com/r/ks468/winepredict
