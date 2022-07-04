# kinesis

Create a Role - Kinesis-Demo-Role
Trusted entity - Ec2
Policy - AmazonS3FullAcces .
         AmazonKinesisFullAccess

Create a Group - kinesis-Demo-Group
		 Policy - AmazonS3FullAcces .
         AmazonKinesisFullAccess

Create a user - kinesis-Demo-user
      -programmetric Access
	  - Console Access
Make the user part of "kinesis-Demo-Group"

Create a Amazon Linux Ec2 instance t2.micro
Attach the Role -   Kinesis-Demo-Role  to Ec2 instance

Open the kinesis
click on create a kinesis data stream

Data stream name - kinesis-demo
provisioned
No. of Open shard - 1

click on create data stream

Now we will create a Kinesis Data Firehose becuase , once the streaming service will get the data from logs , then we need to push the data
into somewhere.

From kinesis data stream , we cannot push the data.

some consumer need to be there in the pipeline. so we are adding the kinesis Data Firehose.

Create a Kinesis Data Firehose
-------------------------------

select the kinesis Data Firehose and click on create

Source - Amazon Kinesis Data Stream
Destination - Amazon S3

Kinesis Data stream - kinesis-demo ( Select the kinesis Data stream which we have created earlier)

Delievery Stream Name - Kinesis-Firehose-Demo


Data Transformation - Disabled

Record format conversion - Disabled


Destination - Select S3

S3-Bucket -> select s3 bucket - kinesis-demo-123454321 ( or create a new one)
click on next

Buffer Intervel - 60

permission - Choose a new role  (if you have not Existing role)

click on next

click on create delievery stream.


Login to Ec2 instance

sudo su

yum install git

Now we need to install kinesis agent

https://docs.aws.amazon.com/streams/latest/dev/writing-with-agents.html#download-install

sudo yum install –y https://s3.amazonaws.com/streaming-data-agent/aws-kinesis-agent-latest.amzn1.noarch.rpm

after kinesis agent installation , you should able to see some agent file json files.

git clone https://github.com/awsdevops009/kinesis.git



Modify the agent.json file and replace the new agent.json file inside /etc/aws-kinesis

Replace your AccessKey and secretKey in this file

agent.json

{
  "cloudwatch.emitMetrics": true,
  "kinesis.endpoint": "",
  "firehose.endpoint": "",

  "awsAccessKeyId": "AKIA5G34BLK3KRZARQWY",
  "awsSecretAccessKey": "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",

  "flows": [
    {
      "filePattern": "/var/log/weather/*.log",
      "kinesisStream": "kinesis-demo",
      "partitionKeyOption": "RANDOM",
      "dataProcessingOptions": [
         {
            "optionName": "CSVTOJSON",
            "customFieldNames": ["Region", "Country", "State","City", "Month", "Day", "Year", "AvgTemperature"]
         }
      ]
    }
  ]
}


Go to the /etc/aws-kinesis

rm -rf agent.json

cd /home/ec2-user/kinesis-demo/
cp cp agent.json //etc/aws-kinesis/

Replace the agent.json file with new one.

Now you are in /etc/aws-kinesis

Now start the agent service

sudo service aws-kinesis-agent start

To see service has started or not

cd /var/log/aws-kinesis-agent/

tail -10 aws-kinesis-agent.log



cd /var/log

mkdir weather

cd weather

/var/log/weather

cd /home/ec2-user/kinesis-demo/

cp first.log /var/log/weather



git clone https://github.com/awsdevops009/kinesis-demo.git



wc -l city_temp_streaming_sample.log

head -20 city_temp_streaming_sample.log > first.log

wc -l first.log ( you can see 20 record created)

create a agent.json file  from agent_template.txt

Then modify the access key and secret key


Now Put the first.log file to the info folder

Now if you will see

tail -20 aws-kinesis-agent.log

You can see 20 record successfully


Go to the Kinesis consle , you can verify Kinesis data stream  and click on monitoring section

verify Kinesis Firehose  and monitoring part

verify the S3 bucket , you can see data should be there.



tail-20 city_temp_streaming_sample.log > second.log
cp second.log /var/log/weather

cd /var/log/aws-kinesis-agent/

tail -10 aws-kinesis-agent.log
