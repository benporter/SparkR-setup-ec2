# SparkR-setup-ec2
Commands needed to setup up SparkR using the EC2 scripts

To spin up a Spark Cluster, run the following from the SPARK_HOME folder.  Replace <> with the location and name of the AWS keys.  This launches 1 driver and 1 worker, both running on r3.large boxes, and names the cluster "spark-cluster".  It also creates an AWS security group for the driver and workers with that prefix if it does not already exist.

    ./spark-ec2 --key-pair=<awskeypair> --identity-file=<folder>/<awskeypair>.pem --region=us-east-1 --zone=us-east-1a --instance-type=r3.large --slaves=1 launch spark-cluster

Destroy a spark cluster:


SSH into the spark cluster.  Note, "spark-cluster" corresponds to the argument given to launch when initializing the cluster.

    ./spark-ec2 -k <awskeypair> -i <folder>/<awskeypair>.pem login spark-cluster 
