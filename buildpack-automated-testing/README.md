# Concourse pipelines for buildpack automated testing
This is a pipeline for testing buildpacks automatically when buildpacks are updated in CloudFoundry.

## Setup testing environment
Setup the environment and install Concourse, Gitlab, Artifactory.

Test applications for each buildpack should be ready.

We will build the Java app and upload jar file to Artifactory first.

A repository with all the contents in this folder should be created in Github/Gitlab because it's a parameter for the pipeline.

The following docker images are used by the pipelines. Please host the images locally if the environment doesn't have access to Internet.
* pivotalservices/artifactory-resource
* pcfnorm/rootfs
* maven:3-jdk-8-alpine

## Setup pipelines
Add values in the params.yml file in the sub-directories of this repository. Then execute the following commands to setup pipelines in Concourse.
~~~
fly login -t local -c <concourse-url> -u <username> -p <password>
fly -t local sp -p build_java_app -c build-java-app/pipeline.yml -l build-java-app/params.yml -n
fly -t local unpause-pipeline -p build_java_app
fly -t local sp -p buildpack_automated_testing -c test-buildpacks/pipeline.yml -l test-buildpacks/pipeline.yml -n
fly -t local unpause-pipeline -p buildpack_automated_testing
~~~

## Testing
Trigger the jobs manually in Concourse web UI or execute the commands below.
~~~
fly -t local trigger-job -j buildpack_automated_testing/deploy-staticfile-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-go-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-php-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-ruby-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-binary-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-python-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-nodejs-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-dotnet-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-java-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-java-v4-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-java-offline-app
fly -t local trigger-job -j buildpack_automated_testing/deploy-java-offline-v4-app
~~~

## Destroy pipeline
~~~
fly -t local dp -p build_java_app -n
fly -t local dp -p buildpack_automated_testing -n
~~~
