# SparkR-setup-ec2
Commands needed to setup up SparkR using the EC2 scripts

Background:  this builds on the <a href="http://spark.apache.org/docs/latest/ec2-scripts.html">Running Spark on EC2</a> instructions, including additional commands for getting SparkR up and running. 

Start a Spark Cluster

To spin up a Spark Cluster, run the following from the SPARK_HOME folder.  Replace <> with the location and name of the AWS keys.  This launches 1 driver and 1 worker, both running on <a href="http://aws.amazon.com/ec2/pricing/">r3.large</a> boxes, and names the cluster "spark-cluster".  It also creates an AWS security group for the driver and workers with that prefix if it does not already exist.

    ./spark-ec2 --key-pair=<awskeypair> --identity-file=<folder>/<awskeypair>.pem --region=us-east-1 --zone=us-east-1a --instance-type=r3.large --slaves=1 launch spark-cluster

Destroy a spark cluster.  Note, "spark-cluster" corresponds to the argument given to launch when initializing the cluster.

    ./spark-ec2 destroy spark-cluster

SSH into the spark cluster.  Note, "spark-cluster" corresponds to the argument given to launch when initializing the cluster.

    ./spark-ec2 -k <awskeypair> -i /<folder>/<awskeypair>.pem login spark-cluster 

Write a file to HDFS.  Run from either /root/ephemeral-hdfs/bin or from /root/persistent-hdfs/bin on the driver.  First argument of put is the regular filesystem path, and the second is the HDFS path and fileame.

    ./hadoop fs -mkdir /user/ben
    ./hadoop fs -put /root/spark/README.md /user/ben/readme.txt
    ./hadoop fs -ls /user/ben

Install <a href="https://www.rstudio.com/products/rstudio/download-server/">RStudio</a> on the Amazon Linux driver. R already installed by default on the AMI they used to create the spark cluster.  Access RStudio via http://<driver url>:8787 

    wget https://download2.rstudio.org/rstudio-server-rhel-0.99.451-x86_64.rpm
    sudo yum install --nogpgcheck rstudio-server-rhel-0.99.451-x86_64.rpm

Create a user to log into RStudio with and create a password.  A spark troubleshooting forum question that I can't find a link to claimed that the username that logs into RStudio on the box should be the same username as the one that created the spark cluster from the local machine.  I haven't verified this claim.

    useradd <yourusername>
    passwd <yourusername>

Spark can create many open files during the shuffle step, exceeding the typical OS defaults for a user.  This changes that limit.  <a href="http://askubuntu.com/questions/162229/how-do-i-increase-the-open-files-limit-for-a-non-root-user">more</a>

Check the max available:

    cat /proc/sys/fs/file-max

View the number of allowed open files for the user that is logged in

    ulimit -n
    
Change the number of allowed open files

    ulimit -n <max from previous step>
    
TO DO:  find the config file to remove the ulimit setting change.

RStudio Config

From within RStudio on the driver.

    # load the library, from where Spark is installed
    library("SparkR", lib.loc="/root/spark/R/lib")
    
    #define the Spark home
    Sys.setenv(SPARK_HOME="/root/spark")
    
    #initialize Spark as LOCAL from the driver
    sc <- sparkR.init("local[*]", "SparkR") 

