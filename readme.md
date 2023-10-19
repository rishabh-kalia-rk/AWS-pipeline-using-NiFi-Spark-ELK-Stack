# **Build a Data Pipeline in AWS using NiFi, Spark, and ELK Stack**
# **overview**
&emsp; Building Data Pipeline including using various AWS services and Apache products. We will fetch data from an API, will
transform it and store it. Using apache tools we will perform data analysis. will use some visualization tool also to perform Data visualization on data

## **Key Objectives:**

1. **Data Collection:** will fetch data from an API using Apache NiFi invokedHTTP processor. The data is in JSON format

2. **Data Cleaning and Transformation:** As now data is fetched using the invokedHTTP now we need to get the required fields out of it and convert it to required format. For that we will be using different nifi component or processor.

3. **Data Analysis:** We will be doing analysis using the Apaceh pyspark or by using AWS opensearch service. we will create the EMR cluster to run Apache spark queries and also install logstash to inject data to elastisearch. As the opensearch service provide only kibana and elastic.


4. **Data Visualization:** We will use kibana for data visualization.
<br>

## **Application Architecture**
**Application Architecture used**
1. AWS EC2
2. AWS EMR
3. Apache Nifi
4. Apache Spark
5. AWS opensearch
6. AWS S3


**Programming Language**
* Python
* KQL (Kibana query language)
  

### **Components**
* **AWS EC2**\
 &emsp; It is a web service where an AWS subscriber can request and provision a compute server in AWS cloud. An on-demand EC2 instance is an offering from AWS where the subscriber/user can rent the virtual server per hour and use it to deploy his/her own applications
* **Apache Nifi**\
  &emsp;  NiFi offers a web-based user interface for designing dataflows through a drag-and-drop interface. Users can create complex data workflows by connecting processors, data sources, and data sinks.
* **AWS EMR**\
  &emsp;Amazon EMR cluster:
To process and analyze enormous volumes of data on AWS, big data frameworks like Apache
Hadoop and Apache Spark may be easily operated on Amazon EMR, a managed cluster
platform. You may process data for analytics purposes and business intelligence tasks using
these frameworks and associated open-source projects.

* **Logstash**\
Logstash is an open-source, server-side data processing pipeline that ingests, transforms, and sends data to a variety of outputs. It is part of the Elastic Stack (formerly known as the ELK Stack).
lightweight data processing pipeline that enables you to
gather data from many sources, alter it as you go, and deliver it where you want.
* **Kibana**\
Kibana is a tool for data visualisation and exploration that is used for operational intelligence
use cases, log and time-series analytics, and application monitoring. The popular analytics and
search engine Elasticsearch is tightly integrated with Kibana, making Kibana the go-to tool for
viewing Elasticsearch data.


## **Diagram**
### &emsp; **AWS Work Flow Diagram**
&emsp; ![](./images\workflow.png)

### &emsp; **Nifi Work Flow Diagram**
&emsp; ![](./images\nifi.png)



## **Getting Started**
### &emsp;**Prerequisite**
* create S3 buckets
  * To store the results and intermediate data. 
  
* create a IAM role
  * In this role EC2 service is given permission to access the S3 bucket.
* create key pair to login to services from command line.\
  \
<br>


# **To Start**
## **AWS EC2 instance**
* Create the EC2 instance
* Start the EC2 instance from AWS console
* login to the EC2 instancce.
  * In command line run the command.
    > ssh -i /key/key-pair.pem ec2-user@public-IP-adress
    * /key/key-pair.pem - location where key is dowmloaded
    * ec2-user - user name of the ec2 instance
    * public-IP-adress - from EC2 isntance in details choose Public IP address
* Install OpenJDK 11
  * Apache NiFi, like many other Java-based applications, requires a Java Development Kit (JDK) to be installed on your system in order to run.
    >sudo amazon-linux-extras install java-openjdk11
* Install Apache Nifi
    >sudo wget https://archive.apache.org/dist/nifi/1.10.0/nifi-1.10.0-bin.tar.gz

    >sudo tar -xvf nifi-1.10.0-bin.tar.gz
    * Is used to extract the contents of a compressed tarball fil
    
  
 **Now we have installed the Apache Nifi in the EC2 instance now we will run the apache nifi**
  >sudo nifi-1.10.0/bin/nifi.sh start
  * To start the Apache nifi
  > sudo nifi-1.10.0/bin/nifi.sh status
  * To check if apache nifi is started or not

## **To access Apache Nifi UI**
  * copy the public Id address (Public IP4v address) from your EC2 instance details.
  * In browser URL run
  > http://ip-address:port/nifi
  * ip-address - It is the Public IPv4 address.
  * port - Default nifi access port is 8080


Using the different processor provided by Nifi We will fetch data from an API using Apache NiFi,
transform it and load it in an AWS S3 bucket.
we Convert data from json into csv using Apache NiFi\
After the transformation data i stored in the S3 bucket.


### **AWS EMR cluster**
We need to create EMR cluster to run he spark jobs on the data present in the S3 bucket,
* create AWS EMR cluster.
  
Now we need to login to the EMR cluster.\
In command line run the command
  >ssh -i path/key.pem hadoop@master-DNS
  * path/key.pem - locatoin where key file is downloaded.
  * hadoop - is the default user name to access the AWS EMR.
  * master-DNS - In EMR clsuter in summary copy the Master public DNS.
  
Now we will install logstash in EMR cluster.
AWS provide opensearch service which is managed elastic search service which include kibana and elasctic like services.

## **We can do analysis using AWS opensearch service or using Apache pyspak**

 ## using AWS opensearch service

**Install Java driver**
>sudo yum -y install java-openjdk-devel java-openjdk

**Install logstash**\
To download a tarball file from a URL and save it locally.
>sudo curl
https://artifacts.opensearch.org/logstash/logstash-oss-with-opensearch-output-plugin-7.16.2-linux-x64.tar.gz -o logstash-oss-with-opensearch-output-plugin-7.16.2-linux-x64.tar.gz

To extract the contents of a tarball file.
> sudo tar -zxvf logstash-oss-with-opensearch-output-plugin-7.16.2-linux-x64.tar.gz

**Create the opensearch cluster**
* Create domain
  * Give name to domain
  * version used - Elasticsearch 7.10
  * Instance type used - t3.medium.search
  * create master user
    These credentials will be used to access the opensearch UI. These are admin user and password.
    * master username
    * master password
  
**After creating th opensearch clsuter we get**
* Kibana URL
  * After creating elastic search it provide the Kibana URL. kibana is the visualization tool.
* Domain endpoint
  * This is the elastic search URL.
  * This will be used in config file as value of hosts parameter.


**Create the configuration file so to Inject data from AWS S3 into Amazon OpenSearch**
* To run the Config file
   >sudo logstash-7.16.2/bin/logstash -f ./demo-logstash.conf
  * logstash-7.16.2/bin/logstash - It is the path to logstash folder in bin folder. It will present where logstash is installed. 
  * ./demo-logstash.conf - location where configuration file is present.

**To get to the kibana tool**
* After running the commnad to run the config file. Click on the Kibana URL which will be present in the opensearch cluter.
* which will redirect you to kibana UI. Give the user name and password which you mentioned while creating opensearch. master name and master userto login.
* Once you login on left side on scroll down you will see dev tools which will open the editor to execute the commands.

**Query using Dev tool**\
In management click on Dev tools. A console will open where you can execute query\
Example\
This is no SQL databse so we do API calls to execute queries.  
>GET _cat/indices\
>#this will get the tables
>
>GET accident_demo/_search\
>#this is similary to select statement in sql.

**Query using Stack management**\
In management click on stack management\
We need to create the Index pattern. The index against which we want to execute queries.
* Once the index pattern is created or we can say to get the indexes on which we want to apply query.
* Now go to discover where we will run the query.
 &emsp; ![](./images\discover.png)

* Now we can run the query using KQL kibana query language.
&emsp; ![](./images\query.png)\
  **Example**
  >borough:"Brooklyn" and on_street_name:"LEE AVENUE"

&emsp;&emsp;This command will help to get the record in which borough value is Brooklyn and having street name LEE AVENUE.

**No go to visualize option so we can visualize the query output we can choose different graphs for visualization**
* we need to mention the x-axis and y-axis.
* Using different options from aggredatoin drop down we can get visualize different results. 

## Analyse data using pyspark
* Login to EMR cluster

To get the data in EMR cluster from S3 bucket. Run the command in cmd after login to EMR
> aws s3 cp s3://s3bucket . --resursive
* cp - is the copy command to get the data from source to destination
*  s3://s3bucket - source is the S3 bucket this is the S3 URI link.
* **.** - dot means destination is the current location in EMR clusterd.
* --recursive - If bucket contain folders than copy the object from folders also.
### Run the pyspark shell to executet the spark commands.
In command line run to get in pyspark shell.
>pyspak

By default the pyspark give us the sparksession object as spark. Using spark we can get the methods available in sparksession library.\
Now we can execute the pyspark statements according to our requirement for analysis.\
**Example**\
**In which city or town how many accident occurs - total distinct number of boroughs**
>paths_df = (df_load.select(col("on_street_name")).groupBy('on_street_name').count().sort('count',ascending=False))
>paths_df.filter(paths_df.on_street_name.isNotNull()).show(5)


**This will give top 5 streets where most number of accidents occur.**\
|      on_street_name|&emsp;count|\
|        BELT PARKWAY ->&emsp;   30\
|           FDR DRIVE ->&emsp;   26\
|MAJOR DEEGAN EXPR...->&emsp;   19\
| BRONX RIVER PARKWAY->&emsp;   18\
|            BROADWAY->&emsp;  15

<br>
<br>
<hr style="height:2px;border:none;color:#333;background-color:#333;"