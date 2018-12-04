# Pipeline for build and deploy Java apps #

This is a pipeline to build Java app, upload to Artifactory and then deploy to Cloud Foundry.

## Setup Concourse pipeline ##
Put the pipeline and params.yml under your cloned code repository. Replace the values with actually ones in params.yml.

Valid values for bump-version: major, minor, patch, final.
~~~
cf-api-url: xxx
cf-username: xxx
cf-password: xxx
cf-org: xxx
cf-space: xxx
source-code-repo: git@xxx.git
repo-name: xxx
private-key: |
  -----BEGIN RSA PRIVATE KEY-----
  xxxxxxxx
  -----END RSA PRIVATE KEY-----
artifactory-endpoint: http://xxx/artifactory
artifactory-username: xxxxxxxx
artifactory-password: xxxxxxxx
artifactory-app-repo: "/generic-local"
artifactory-app-regex: "xxx-(?<version>.*).jar"
bump-version: patch
~~~

Setup the pipeline according to the following command.
~~~
fly login -t local -c <concourse-url> -u <username> -p <password>
fly -t local sp -p <pipeline-name> -c concourse-pipeline.yml -l params.yml -n
fly -t local unpause-pipeline -p <pipeline-name>
~~~

In case you want to debug a failed pipeline, use commands like the following.
~~~
fly -t local builds -j <pipeline-name>/<job-name>
fly -t local intercept -j <pipeline-name>/<job-name> -b <build-no>
~~~

If you want to bump up to a major version (current setting is minor), follow the follow procedures:
```
1. Change bump-version value(major) in params.yml and update the pipeline.
2. Code commit to gitlab. The pipeline will be automatically triggered and a new artifact will be pushed to Artifactory.
3. Revert bump-version value in params.yml to the original setting(minor) and update the pipeline.
```