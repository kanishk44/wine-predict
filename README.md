# Wine Quality Prediction over AWS EMR

Training the model
5 nodes will be used to create a cluster, 1 of which will serve as the master node and the others as slave nodes.

After cluster creation S3 bucket will be auto generated. Here we will submit Training.py file and the dataset.

Now we will pull the stored files from S3 bucket to the master node by connecting to the master node.

aws s3 cp s3://aws-logs-877244108283-us-east-1/elasticmapreduce/j-38II9TYTEUBU5/TrainingDataset.csv ./

aws s3 cp s3://aws-logs-877244108283-us-east-1/elasticmapreduce/j-38II9TYTEUBU5/ValidationDataset.csv ./

aws s3 cp s3://aws-logs-877244108283-us-east-1/elasticmapreduce/j-38II9TYTEUBU5/Training.py ./


We will now make these files accessible to slaves:

hadoop fs -put TrainingDataset.csv
			hadoop fs -put ValidationDataset.csv

You may find all the files stored there by typing "ls" and pressing enter.
The following command will install all the necessary libraries after saving them in "requirements.txt" .

	pip install -r requirements.txt

Scala Installation on master node: -
wget https://downloads.lightbend.com/scala/2.12.4/scala-2.12.4.rpm
			sudo yum install scala-2.12.4.rpm
Install Spark:
1.	wget https://dlcdn.apache.org/spark/spark-3.3.1/spark-3.3.1-bin-hadoop3.tgz
2.	sudo tar xvf spark-3.3.1-bin-hadoop3.tgz -C /opt
3.	sudo chown -R ec2-user:ec2-user /opt/spark-3.3.1-bin-hadoop3
4.	sudo ln -fs spark-3.3.1-bin-hadoop3 /opt/spark

Run the Training.py using: - 
		spark-submit Training.py

Wine Quality Prediction
Create an ec2 instance
Install all the required dependencies, Scalav2 and Sparkv2.
Copy the trained the model from S3 and paste it in Ec2 instance

aws s3 cp s3://aws-logs-766621730595-us-east-1/elasticmapreduce/j-38II9TYTEUBU5/trainingmodel.model ./ --recursive
Unzip the model and move the contents to the new folder: -  

tar -xzvf model.tar.gz
mkdir model
mv data model
mv metadata model

Update the path Environment: -

vim ~/.bash_profile
copy following lines into file and then save it
export SPARK_HOME=/opt/spark
PATH=$PATH:$SPARK_HOME/bin
export PATH
Run the Testing file: - 
spark-submit test.py


Docker Implementation: - 
Pull the image from my docker repo using this command:
docker pull ks468/winepredict:latest

Run the docker image using this command:
docker run --volume /path/to/program/directory:/home/myuser/ ks468/winequal #this shares the volumes with local for running the app.

Link to docker repo:
https://hub.docker.com/r/ks468/winepredict



![image](https://user-images.githubusercontent.com/48882827/206627020-6e89e6cd-75e4-40c6-9cfd-ff609e9ec950.png)
