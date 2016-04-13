#!groovy

/**
  * This file is being used by an initial Jenkins freestyle job that I have named "seed".
  * The initial "seed" job has a single "Process Job DSLs" build step which looks on the filesystem for "Jenkinsfile" - i.e this file.
  *
  * Purpose:
  * - want to use Jenkins 2.0 Pipeline to automatically build branches and pull-requests.
  * - each pull-request should get a comment about build status result whenever there is a previously unbuilt commit. 
  *
  * Description:
  * - create a Jenkins 2.0 "Github Organization" top-level job folder.
  * - populates sub-folder structure and generates "Multibranch Pipeline" jobs for any Github repo branches and pull requests where "Jenkinsfile" is found in a repo root.
  *
  * See:
  * - https://jenkins.io/2.0/
  * - https://jenkins.io/doc/pipeline/jenkinsfile/
  * - https://jenkins.io/doc/pipeline/
  * - https://jenkins.io/solutions/pipeline/
  *
  * - https://www.cloudbees.com/blog/need-jenkins-pipeline
  * - https://wiki.jenkins-ci.org/display/JENKINS/CloudBees+GitHub+Branch+Source+Plugin  (provides the Multibranch Pipeline functionality and Github pull request building)
  * - https://developer.github.com/v3/repos/statuses/
  *
  * - https://documentation.cloudbees.com/docs/cookbook/_continuous_delivery_with_jenkins_pipeline.html
  * - https://documentation.cloudbees.com/docs/cookbook/pipeline-as-code.html
  * - https://documentation.cloudbees.com/docs/cje-user-guide/github-branch-source.html
  * - https://documentation.cloudbees.com/docs/cje-user-guide/workflow.html
  * 
 **/

import javaposse.jobdsl.dsl.*

// Include access to Jenkins.instance for inspecting e.g `Jenkins.instance.allItems.each{p-> println "job:" +p.name}` or `Jenkins.instance.getItemByFullName('somejobname')`
import jenkins.model.*
import hudson.model.*

def jobName = 'Example'
def jobDescription = 'Jenkins 2.0 Pipeline example seed of Github folder structure'
def githubUser = 'runelabs'
def githubOrgUrl = 'https://github.com/runelabs'
def githubOrgName = 'Rune Karlsen'
def githubOrgAvatar = 'https://avatars.githubusercontent.com/u/471666?v=3'

// Need to use Jenkins Credentials global secret username-password for Github username and Github Personal Access Token as follows:
// https://github.com/settings/tokens/new?scopes=repo,public_repo,admin:repo_hook,admin:org_hook&description=Jenkins+Access
def jenkinsGithubuserAccesstokenId = 'jenkins2-example'

def seedJob = job(jobName) {
    displayName jobName
    description("$jobDescription")
    configure { project ->
        // This job config.xml is for a Jenkins 2.0 type Github Organization Folder
        project.name = 'jenkins.branch.OrganizationFolder'
        // Jenkins will automatically pin plugin version attributes for the config.xml, but can also set attributes for top node like this: project.attributes()['attrib'] = 'something'
        project / 'projectFactories' / 'org.jenkinsci.plugins.workflow.multibranch.WorkflowMultiBranchProjectFactory' {}
        project / 'actions' {
            'org.jenkinsci.plugins.orgfolder.github.GitHubOrgAction' {
            	url "$githubOrgUrl"
           		name "$githubOrgName"
                avatar "$githubOrgAvatar"
        	}
            'org.jenkinsci.plugins.orgfolder.github.GitHubLink' {
            	image 'logo'
            	url "$githubOrgUrl"
            }
        }
        project / 'icon' (class: 'org.jenkinsci.plugins.orgfolder.GithubOrgIcon') / 'folder' (class: 'jenkins.branch.OrganizationFolder', reference: '../..') // sets class and reference attributes
        project / 'properties' / 'com.cloudbees.hudson.plugins.folder.properties.FolderCredentialsProvider_-FolderCredentialsProperty' {
            domainsCredentialsMap (class: 'hudson.util.CopyOnWriteMap$Hash') {
                entry {
                    'com.cloudbees.plugin.credentials.domains.Domain' { 'specifications' {} }
                    'java.util.concurrent.CopyOnWriteArrayList' {}
                }
            }
        }
        project / 'navigators' / 'org.jenkinsci.plugins.github__branch__source.GitHubSCMNavigator' {
            repoOwner "$githubUser"
            scanCredentialsId "$jenkinsGithubuserAccesstokenId"
            checkoutCredentialsId 'SAME'
            pattern '.*'
        }
        project << {
            primaryView 'Repositories'
        	viewsTabBar (class: 'hudson.views.DefaultViewsTabBar', reference: '..')
            views {
                'hudson.model.ListView' {
            		owner (class: 'jenkins.branch.OrganizationFolder', reference: '../../..')
            		name 'Repositories'
            		filterExecutors false
            		filterQueue false
            		properties (class: 'hudson.model.View$PropertyList')
            		jobNames {
                		comparator (class: 'hudson.util.CaseInsensitiveComparator')
            		}
            		jobFilters {}
            		columns {
                		'hudson.views.StatusColumn' {}
                		'hudson.views.WeatherColumn' {}
                		'org.jenkinsci.plugins.orgfolder.github.CustomNameJobColumn' {
                    		bundle 'org.jenkinsci.plugins.orgfolder.github.Messages'
                    		key 'ListViewColumn.Repository'
                		}
                		'org.jenkinsci.plugins.orgfolder.github.RepositoryDescriptionColumn' {}
            		}
            		includeRegex '.*'
            		recurse false
                }
        	}
        }
        project / 'triggers' / 'com.cloudbees.hudson.plugins.folder.computed.PeriodicFolderTrigger' {
            spec 'H H * * *'
            interval 86400000
        }
        project / 'orphanedItemStrategy' (class: 'com.cloudbees.hudson.plugins.folder.computed.DefaultOrphanedItemStrategy') {
            pruneDeadBranches true
            daysToKeep 0
            numToKeep 0
        }
        project / 'healthMetrics' / 'com.cloudbees.hudson.plugins.folder.health.WorstChildHealthMetric'

        // For Jenkins 2.0 Organization Folder job - some of the FreestyleJob generated config.xml is not needed.
        project.remove(project / scm)
        project.remove(project / publishers)
        project.remove(project / builders)
        project.remove(project / buildWrappers)
        project.remove(project / concurrentBuild)
        project.remove(project / blockBuildWhenDownstreamBuilding)
        project.remove(project / blockBuildWhenUpstreamBuilding)
        project.remove(project / keepDependencies)
        project.remove(project / canRoam)
        project.remove(project / disabled)
    }
}

// Want to immediately schedule a build of the new job for computing sub-folders so that branches and pull-requests of any origin get included.
queue(seedJob) // https://github.com/jenkinsci/job-dsl-plugin/wiki/Job-DSL-Commands
