# Rundeck S3 Log Storage Plugin

This is a plugin for [Rundeck](http://rundeck.org) that uses [Amazon S3](http://aws.amazon.com/s3) to store execution
log files, for backup or for cloud-friendly behavior.

## Build

    ./gradlew clean build

## Install

Copy the `rundeck-s3-log-plugin-x.y.jar` file to the `libext/` directory inside your Rundeck installation.

Enable the ExecutionFileStorage provider named `org.rundeck.amazon-s3` in your `rundeck-config` file:

    rundeck.execution.logs.fileStoragePlugin=org.rundeck.amazon-s3

## AWS Credentials

The plugin will by default use the "credentials provider chain" for AWS access credentials, which allows you to
externally configure the credentials in any of three ways:

1. Environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_KEY`
2. Java system properties `aws.accessKeyId` and `aws.secretKey`
3. Instance Profile credentials, if you are running on EC2. (See [the IAM user guide][1]).

[1]: http://docs.aws.amazon.com/IAM/latest/UserGuide/role-usecase-ec2app.html

If you want to specify access key and secret key, you can do so in the configuration:

## Configuration

To configure the AWS access credentials you can set these property values:

`AWSAccessKeyId` : access key, required if using `AWSSecretKey`

`AWSSecretKey` : secret key, required if using `AWSAccessKeyId`

`AWSCredentialsFile` : properties file which contains `accessKey` and `secretKey` entries.  Alternative to specifying
the `AWSAccessKeyId and `AWSSecretKey`

S3 configuration uses these plugin configuration property values:

`bucket` : name of the S3 bucket to use

`path` :  a path-like string that defines where in the bucket to store the log for a particular execution.  You can
 include variables to expand. Default value: `rundeck/project/$PROJECT/logs/$ID`

Variables in the `path` value include:

* `${job.execid}` - the execution ID
* `${job.project}` - the project name
* `${job.id}` - the Job UUID if it exists

`region` : AWS region name to use. Default: `us-east-1`

You can define the configuration values in `framework.properties` by prefixing the property name with the stem:
 `framework.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.`.  Or in a project's project.properties file with the stem
 `project.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.`.

For example:

    #AWSAccessKeyId and AWSSecretKey can be specified in the file
    framework.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.AWSAccessKeyId=ABC123...
    framework.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.AWSSecretKey=ABC321...

    #alternately, AWSCredentialsFile can point to a file which contains `accessKey` and `secretKey`
    framework.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.AWSCredentialsFile=/path/to/awscredentials.properties

    #name of the bucket
    framework.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.bucket=test-rundeck-logs

    #path to store the logs
    framework.plugin.ExecutionFileStorage.org.rundeck.amazon-s3.path=logs/${job.project}/${job.execid}.log
