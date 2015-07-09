# SparkR-setup-ec2
Commands needed to setup up SparkR using the EC2 scripts

Start a Spark Cluster

To spin up a Spark Cluster, run the following from the SPARK_HOME folder.  Replace <> with the location and name of the AWS keys.  This launches 1 driver and 1 worker, both running on <a href="http://aws.amazon.com/ec2/pricing/">r3.large</a> boxes, and names the cluster "spark-cluster".  It also creates an AWS security group for the driver and workers with that prefix if it does not already exist.

    ./spark-ec2 --key-pair=<awskeypair> --identity-file=<folder>/<awskeypair>.pem --region=us-east-1 --zone=us-east-1a --instance-type=r3.large --slaves=1 launch spark-cluster

Destroy a spark cluster.  Note, "spark-cluster" corresponds to the argument given to launch when initializing the cluster.

    ./spark-ec2 destroy spark-cluster

SSH into the spark cluster.  Note, "spark-cluster" corresponds to the argument given to launch when initializing the cluster.

    ./spark-ec2 -k <awskeypair> -i <folder>/<awskeypair>.pem login spark-cluster 

Write a file to HDFS.  Run from either /root/ephemeral-hdfs/bin or from /root/permanant-hdfs/bin on the driver.  First argument of put is the regular filesystem path, and the second is the HDFS path and fileame.

    ./hadoop fs -mkdir /user/ben
    ./hadoop fs -put /root/spark/README.md /user/ben/readme.txt
    ./hadoop fs -ls /user/ben
