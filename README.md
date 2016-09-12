## Introduction

My plan was to create a script (`aws-app.sh`) that showcases both the use of Docker container technology and end-to-end automation of an EC2 instance life cycle. (I had previously created a Docker image and uploaded it to Docker Hub. The image has a trivial web application just to show that the Docker container is up and running.)

## The Steps

Below are the steps.

1. Building a Docker image that hosts a demo web app. This task included:
   * Building a Docker image for the demo web app. (It is important that the Docker image is built from the directory where the demo web app is, so that the referenced files are available. The Dockerfile must also exist in this directory.)
   * Loading the Docker image to Docker Hub (`tledocker/waveweb`)
1. Developing a script that automates the following:
   * Configuring AWS access
   * Creating a key pair for ssh access
   * Creating a security group to allow ssh access (port 22) and access to the demo web app (port 8000)
   * Creating an EC2 instance to host the Docker image, that considers the above key pair and security group
   * Instantiating and running a Docker container on the EC2 instance, that hosts the demo web app
   * Displaying the URL to be used for verification of the demo web app
   * Removing the created key pair, security group and the EC2 instance, and local generated files

See the comments inside the `aws-app.sh` script itself for more technical details.

## Example of a Script Run

Below is the output of a sample run of the `aws-app.sh` script:
```
# ./aws-app.sh 

INFO: This section is to gather AWS access details.
INFO: To simplify the process, please provide the access details anyway
INFO: even if you have already run 'aws configure'.
INFO: If you have set environment variables AWS_ACCESS_KEY_ID and
INFO: AWS_SECRET_ACCESS_KEY you will not be prompted.

Enter your aws access key id: xxxxxxxxxxxxxxxxxxxxxxxxxx
Enter your aws secret access key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxx

INFO: Creating key pair...

INFO: Private key file is tle-key-pair.pem

INFO: Creating security group...

INFO: The created security group ID is sg-f0f3f496

INFO: Creating an EC2 instance...

INFO: Waiting until the EC2 instance is up and running...
INFO: EC2 instance ID is i-93b30507

INFO: Deploying the web app inside a Docker container running on EC2 instance...
INFO: Making sure ssh on ec2-54-186-215-216.us-west-2.compute.amazonaws.com is ready before proceeding...
INFO: Waiting another 5 seconds for ssh on ec2-54-186-215-216.us-west-2.compute.amazonaws.com to become ready...
INFO: Waiting another 5 seconds for ssh on ec2-54-186-215-216.us-west-2.compute.amazonaws.com to become ready...
INFO: Waiting another 5 seconds for ssh on ec2-54-186-215-216.us-west-2.compute.amazonaws.com to become ready...
INFO: Waiting another 5 seconds for ssh on ec2-54-186-215-216.us-west-2.compute.amazonaws.com to become ready...
INFO: ssh on ec2-54-186-215-216.us-west-2.compute.amazonaws.com is ready. Proceeding...

INFO: Deploying the web app in a Docker container running on EC2 instance...

Starting cgconfig service: [  OK  ]
Starting docker:	.[  OK  ]
Using default tag: latest
latest: Pulling from tledocker/waveweb
2f0243478e1f: Pulling fs layer
d8909ae88469: Pulling fs layer
820f09abed29: Pulling fs layer
01193a8f3d88: Pulling fs layer
8aa3f9f71966: Pulling fs layer
77d70d82bdac: Pulling fs layer
673ca0810c34: Pulling fs layer
e42680486a27: Pulling fs layer
657271f6a788: Pulling fs layer
b18456731059: Pulling fs layer
15515f571117: Pulling fs layer
01193a8f3d88: Waiting
8aa3f9f71966: Waiting
77d70d82bdac: Waiting
673ca0810c34: Waiting
e42680486a27: Waiting
657271f6a788: Waiting
b18456731059: Waiting
15515f571117: Waiting
820f09abed29: Verifying Checksum
820f09abed29: Download complete
d8909ae88469: Verifying Checksum
d8909ae88469: Download complete
01193a8f3d88: Verifying Checksum
01193a8f3d88: Download complete
2f0243478e1f: Verifying Checksum
2f0243478e1f: Download complete
8aa3f9f71966: Verifying Checksum
8aa3f9f71966: Download complete
673ca0810c34: Verifying Checksum
673ca0810c34: Download complete
e42680486a27: Verifying Checksum
e42680486a27: Download complete
657271f6a788: Verifying Checksum
657271f6a788: Download complete
b18456731059: Verifying Checksum
b18456731059: Download complete
15515f571117: Verifying Checksum
15515f571117: Download complete
2f0243478e1f: Pull complete
77d70d82bdac: Verifying Checksum
77d70d82bdac: Download complete
d8909ae88469: Pull complete
820f09abed29: Pull complete
01193a8f3d88: Pull complete
8aa3f9f71966: Pull complete
77d70d82bdac: Pull complete
673ca0810c34: Pull complete
e42680486a27: Pull complete
657271f6a788: Pull complete
b18456731059: Pull complete
15515f571117: Pull complete
Digest: sha256:5afc7ce10a162cd0e8f7a5c01be55d6f6e8032934ebb11c4005ea9023c71bbe8
Status: Downloaded newer image for tledocker/waveweb:latest
df577456daa844102b951443f14d931c9ada4f6ad78efd4b525fd98bd92f1e71

INFO: Point your web browser to http://ec2-54-186-215-216.us-west-2.compute.amazonaws.com:8000 to verify

Enter "Y" to clean up things on AWS and in local directory prior to exiting: Y

INFO: Stopping EC2 instance...
INFO: Terminating EC2 instance...

INFO: Deleting key pair...

INFO: Deleting security group...
INFO: Waiting another 10 seconds then delete it again (due to dependency)...
INFO: Waiting another 10 seconds then delete it again (due to dependency)...
INFO: Waiting another 10 seconds then delete it again (due to dependency)...
INFO: Waiting another 10 seconds then delete it again (due to dependency)...
INFO: Waiting another 10 seconds then delete it again (due to dependency)...
INFO: Security group "tle-security-group" deleted

INFO: Deleting local temporary files...

INFO: Program has ended.
```
