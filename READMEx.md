##Stage TEN: HOW TO BUILD AND DEPLOY A WOWZA STARTUP PACKAGE

The following procedure describes how to:
- build and publish a wowza startup package, and
- use it to deploy one or more wowza server instances on aws

###1. Build a wowza startup package

Building a wowza startup package includes two steps:

1. Download the project source code
2. Build and publish the build artifacts

**1.1. Download the *stageten-wowza-app* project from GitHub**
```
   $ cd <your-temporary-directory>
   $ git clone https://github.com/lazarentertainment/stageten-wowza-app
   (Enter your credentials when prompted)
```
The project source code should now be downloaded into your local directory.

**1.2. Build and publish build artifacts**
```
   $ cd stageten-wowza-app
   $ ./build_for_aws.sh build
```
   At the end of this step, all build artifacts--which include those of the wowza app and wowza startup package--should be generated and published.

###2. Deploy one or more wowza server instances on aws

Wowza server instance deployment includes two steps:

1. Editing the deployment configuration file
2. Deploying one or more wowza server instances per the deployment configuration file

**2.1. Editing the deployment configuration file**

   Default deployment configuration is provided but not recommended for production work. See **APPENDIX A** for details.
   In this example, the *vi* editor is used, but you can just use any editor.
```
   $ vi lazarMediaServer/build/wowza-startup-package/deploy_wowza_startup_package.conf
```
**2.2. Deploy one or more wowza server instances based on the deployment configuration file**
```
   $ ./build_for_aws.sh deploy
```
   On a small system (such as *t2.small*), it took approximately 10 minutes for a wowza server to become fully functional. With more powerful systems and VPC endpoint implementation, it should take less time.

   You should be able to login to the deployed wowza server instances via *ssh* and to verify.


##APPENDIX A

###1. The Deployment Configuration file

The deployment configuration file contains the parameters needed to deploy a wowza server instance on aws. After the *Build a wowza startup package* step is done. Assuming you are still in the *stageten-wowza-app* directory, the content of this file should look like this:
```
   $ cat lazarMediaServer/build/wowza-startup-package/deploy_wowza_startup_package.conf
   #
   # Configuration file for deploying a wowza startup package on a pre-built wowza instance or instances in aws
   #
   REGION=                    # AWS region, eg., us-east-1
   IMAGE_ID=                  # Name of an aws wowza ami image (eg., ami-1190597c)
   KEY_NAME=                  # Name of your key pair
   SECURITY_GROUP_IDS=        # Security group id(s)
   INSTANCE_TYPE=             # Type of ami image, eg., t2.small
   SUBNET_ID=                 # ID of target subnet, eg., subnet-235a1309
   COUNT=                     # Number of instances to deploy
   IAM_INSTANCE_PROFILE=      # IAM instance profile defined in AWS
   STARTUP_PKG_URL=           # URL of wowza startup package - should use the default value (see below)
   WOWZA_LICENSE_STR=         # Wowza license string
   #
   # Default values if not specified above
   #
   REGION=${REGION:=us-east-1}
   IMAGE_ID=${IMAGE_ID:=ami-1190597c}
   KEY_NAME=${KEY_NAME:=tle-wowza}
   SECURITY_GROUP_IDS=${SECURITY_GROUP_IDS:=sg-54350a2e}
   INSTANCE_TYPE=${INSTANCE_TYPE:=t2.small}
   SUBNET_ID=${SUBNET_ID:=subnet-235a1309}
   COUNT=${COUNT:=1}
   IAM_INSTANCE_PROFILE=${IAM_INSTANCE_PROFILE:=stageten-wowza-startup-pkg}
   USER_DATA_URL=file://user-data.txt
   STARTUP_PKG_URL=${STARTUP_PKG_URL:=<URL-of-startup-package>}
   WOWZA_LICENSE_STR=${WOWZA_LICENSE_STR:=<wowza-license-string>}
   #
```
**NOTES**:
*REGION*: must be the region where the image (IMAGE_ID), key pair (KEY_NAME), security groups (SECURITY_GROUP_IDS), VPC (SUBNET_ID) were created
*STARTUP_PKG_URL*: is the URL of the wowza startup package, for example, *https://s3.amazonaws.com/repository.systems.stageten.tv/releases/tv/stageten/wowza-app/1.5.1/wowza_startup_package-1.5.1.zip*
*WZA_LICENSE_STR*: is the wowza license key provided by wowza. If a WOWZA Streaming Server is installed on the local system, the */usr/local/WowzaStreamingEngine/conf/Server.license* file should contain a license key

The second section defines the default values for these parameters. It is recommended that *STARTUP_PKG_URL* not be changed as it normally points to the latest version of the wowza startup package.

###2. Changeset that implements the wowza startup package build and deloyment process

- [Updated] *stageten-wowza-app/aws_startup/ffmpeg/install_ffmpeg.sh*: script to install FFmpeg tool set on target wowza server instance
- [Updated] *stageten-wowza-app/aws_startup/startup.xml*: installation configuration file for wowza startup package
- [Updated] *stageten-wowza-app/lazarMediaServer/build.gradle*: build file for compilation, publishing and deployment of wowza startup package and related artifacts
- [Updated] *stageten-wowza-app/lazarMediaServer/settings.gradle*: settings file for wowza app project name
- [Added  ] *stageten-wowza-app/aws_startup/ffmpeg/wowza4.5-byol-ami-1190597c*: directory of FFmpeg tool set compiled on an *aim-1190597* instance
- [Added  ] *stageten-wowza-app/aws_startup/tuning/tune.sh*: script to reconfigure media cache size on target wowza server instance
- [Added  ] *stageten-wowza-app/build_for_aws.sh*: script to build/publish or deploy wowza startup package, or both
- [Added  ] *stageten-wowza-app/lazarMediaServer/build-wowza-startup-package*, which holds the following files:
              -- *README.md*: this file
              -- *build_ffmpeg_for_wowza_ami.sh*: script to build the FFmpeg tool set for CentOS (compatible with Amazon Linux)
              -- *create_wowza_startup_package.sh*: script to create a wowza startup package
              -- *deploy_wowza_startup_package.sh*: script to deploy the wowza startup package to a wowza server instance on aws
              -- *deploy_wowza_startup_package.conf*: configuration file used to deploy the wowza startup package to a wowza server instance on aws

###3. Location of published build artifacts

- Wowza app artifacts are stored in the S3 bucket *repository.systems.stageten.tv*, and can be downloaded from: *https://s3.amazonaws.com/repository.systems.stageten.tv/releases/tv/stageten/wowza-app/<release>/wowza-app-<release>.jar*. The URL's of the corresponding message digest files end with .md5 and .sha1 respectively.
- Wowza startup package artifacts are stored in the S3 bucket *repository.systems.stageten.tv*, and can be downloaded from: *https://s3.amazonaws.com/repository.systems.stageten.tv/releases/tv/stageten/wowza-app/<release>/wowza_startup_package-<release>.zip*. The URL's of the corresponding message digest files end with .md5 and .sha1 respectively.
