# jenkins2-pipeline-multibranch-seed-example

 Trying the new Jenkins 2.0 Pipeline plugin suite...
 
 The `Jenkinsfile` is being used by an initial Jenkins freestyle job that I have named "seed".
 
 The initial "seed" job has a single "Process Job DSLs" build step which looks on the filesystem for `Jenkinsfile`.

## Purpose:
 - want to use Jenkins 2.0 Pipeline to automatically build branches and pull-requests.
 - each pull-request should get a comment about build status result whenever there is a previously unbuilt commit.    

## Description:
 - create a Jenkins 2.0 "Github Organization" top-level job folder.
 - populates sub-folder structure and generates "Multibranch Pipeline" jobs for any Github repo branches and pull requests whenever `Jenkinsfile` is found in a repo root.

## See:
 - https://jenkins.io/2.0/
 - https://jenkins.io/doc/pipeline/jenkinsfile/
 - https://jenkins.io/doc/pipeline/
 - https://jenkins.io/solutions/pipeline/

 - https://www.cloudbees.com/blog/need-jenkins-pipeline

 - https://wiki.jenkins-ci.org/display/JENKINS/CloudBees+GitHub+Branch+Source+Plugin <br/> _(provides the "Multibranch Pipeline" functionality and Github pull request building)_
 - https://developer.github.com/v3/repos/statuses/

 - https://documentation.cloudbees.com/docs/cookbook/_continuous_delivery_with_jenkins_pipeline.html
 - https://documentation.cloudbees.com/docs/cookbook/pipeline-as-code.html
 - https://documentation.cloudbees.com/docs/cje-user-guide/github-branch-source.html
 - https://documentation.cloudbees.com/docs/cje-user-guide/workflow.html

