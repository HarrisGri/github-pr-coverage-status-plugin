# GitHub Pull Request Coverage Status Plugin for Jenkins

[![Build Status](https://jenkins.ci.cloudbees.com/job/plugins/job/github-pr-coverage-status-plugin/badge/icon)](https://jenkins.ci.cloudbees.com/job/plugins/job/github-pr-coverage-status-plugin/)
[![Coverage Status](https://coveralls.io/repos/github/terma/github-pr-coverage-status/badge.svg?branch=master)](https://coveralls.io/github/terma/github-pr-coverage-status?branch=master)

* [Overview](#overview)
* [Supports coverage reports](#supports-coverage-reports)
* [How to use](#how-to-use)
  * [Install Plugin](#install-plugin)
  * [Configure access to GitHub](#configure-access-to-github)
  * [Configure master coverage source](#configure-master-coverage-source)
    * [Jenkins build](#jenkins-build)
    * [Sonar](#sonar)
  * [Publish coverage for Pull Request](#publish-coverage-for-pull-request)
    * [GitHub Pull Request Builder Plugin](#github-pull-request-builder-plugin)
    * [Branch API Plugin](#branch-api-plugin)
    * [Other](#other)
* [How to use with Jenkins Pipelines](#how-to-use-with-jenkins-pipelines)    
* [Troubleshooting](#troubleshooting)
* [Changelog](#changelog)

## Overview

https://wiki.jenkins-ci.org/display/JENKINS/GitHub+PR+Coverage+Status+Plugin

Parse code coverage report generated by build and post code coverage status comment to GitHub pull request:
![Example](https://raw.githubusercontent.com/jenkinsci/github-pr-coverage-status-plugin/master/screenshot.png)

## Supports coverage reports
* Jacoco ```jacoco.xml```
* Cobertura ```cobertura.xml``` or ```cobertura-coverage.xml```
* Clover ```clover.xml```
* [SimpleCov JSON (Ruby)](https://github.com/vicentllongo/simplecov-json) ```coverage.json```
 
## How to use

### Install Plugin

* Install plugin from Jenkins Plugin Repository
* Or manually 
  * Goto [releases](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases)
  * Download latest version of plugin *.hpi* file
  * Install to your Jenkins [guide](https://wiki.jenkins-ci.org/display/JENKINS/Plugins)

### Configure access to GitHub

* Goto ```Manage Jenkins```
* Find section ```Coverage status for GitHub Pull Requests```
* Configure global settings of plugin
  * GitHub API URL keep blank for GitHub.com and fill if for dedicated instance of GitHub, example: ```http(s)://hostname/api/v3/```
  * Set [Personal Access Token](https://github.com/blog/1509-personal-api-tokens) (or keep blank if anonymous access enabled)

### Configure master coverage source

To be able compare Pull Request coverage plugin needs master coverage for GitHub repository. 

#### Jenkins build

* Add *Record Master Coverage* post build step to build which tests your master. 
Coverage will be stored per repository URL

#### Sonar

You have the option to get the master coverage (base coverage) from your SonarQube instance. Otherwise the plugin will keep track of the master coverage in the project configuration file.

* Goto ```Manage Jenkins```
* Find section ```Coverage status for GitHub Pull Requests```
* Fill ```Sonar URL``` field - e.g. `http://sonar.mycompany.com`
* Turn On ```Use Sonar for master coverage```
* Optionally use can specify your personal ```Sonar access token```. 
* The next pull request build will use SonarQube as the the source for master coverage

The plugin will try to find the project in SonarQube based on the repository name. If more than one projects match, a warning is logged, and the first one will be used to get the coverage data. 

### Publish coverage for pull request

Depends on plugin which you are using to run (trigger) pull request in Jenkins.

#### GitHub Pull Request Builder Plugin

* To Jenkins Job triggered by [GitHub pull request builder plugin](https://wiki.jenkins-ci.org/display/JENKINS/GitHub+pull+request+builder+plugin) add *Publish coverage to GitHub* post build step
* Ensure that build create code coverage report file

#### Branch API Plugin

* To Jenkins Job triggered by [Branch API Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Branch+API+Plugin) add *Publish coverage to GitHub* post build step
* Ensure that build create code coverage report file

#### Other

* Before ```Publish coverage to GitHub``` step inject ```CHANGE_ID``` and ```CHANGE_URL``` to build envs
where first one is Pull Request ID (number) and second link to repository
* Add *Publish coverage to GitHub* post build step to build
* Ensure that build create code coverage report file

## How to use with Jenkins Pipelines
* After running tests set build result to Success
    * ```currentBuild.result = 'SUCCESS'```
* Trigger MasterCoverageAction to collect master coverage
    *  ```step([$class: 'MasterCoverageAction'])```
* Trigger CompareCoverageAction to compare coverage and publish results
    *  ```step([$class: 'CompareCoverageAction'])```
    * Optionally use can specify here sonar login and sonar password like this:
        ```step([$class: 'CompareCoverageAction', sonarLogin: "login", sonarPassword: "password"])```

## Troubleshooting

### No coverage picture and my Jenkins is in private network and not accessible for GitHub

Because of that GitHub can't render icon picture hosted on Jenkins. Plugin can use http://shields.io public resource instead of Jenkins hosted picture. To use it: 

* Goto ```Jenkins Configuration``` 
* Turn on ```Jenkins is not accessible for GitHub```
* Done next coverage comment in Pull Request will use http://shields.io

### No coverage picture on GitHub Pull Request Page

If you see next picture on your pull request in GitHub:

![No coverage picture](https://raw.githubusercontent.com/jenkinsci/github-pr-coverage-status-plugin/master/no-coverage-picture.png)

In most cases that mean that your Jenkins runs on ```http``` instead of ```https```
Plugin uses same protocol as your Jenkins, however GitHub in most cases accessible by ```https```
Default browser policy to block non secure resources on secure pages from unknown domain.

To fix that:

1. Configure Jenkins to be accessible for ```https``` https://wiki.jenkins-ci.org/display/JENKINS/Starting+and+Accessing+Jenkins
1. Next you have a few options:
  * Run your Jenkins on ```https``` only
  * Or configure plugin to publish link on picture over ```https```
    * Open Jenkins
    * Click ```Manage Jenkins```
    * Click ```Configure System```
      * Find section ```Coverage status for GitHub Pull Requests```
      * Find property ```Jenkins URL for icon in Pull Request```
      * Put URL to your Jenkins with ```https``` like ```https://jenkins.my.com```
      * Save
    * Restart Jenkins

![Plugin settings](https://raw.githubusercontent.com/jenkinsci/github-pr-coverage-status-plugin/master/plugin-settings.png)

## Changelog

### [1.9.1](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.9.1)

- Allow to disable SimpleCov Coverage parser

### [1.9.0](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.9.0)

- Add support of Workflow SCM Step Plugin

### [1.8.2](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.8.2)

- Fix Master showing 0% even after RecordMasterCoverage is run

### [1.8.0](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.8.0) 

- Sonar token & login/password authentication

### [1.7.1](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.7.1)

- SimpleCov JSON report fix coverage

### [1.7.0](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.7.0)

- Support of https://github.com/jenkinsci/branch-api-plugin
- Support for Jenkins Pipeline

### [1.6.1](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.6.1)

- Support single quotes for Cobertura Report

### [1.6.0](https://github.com/jenkinsci/github-pr-coverage-status-plugin/releases/tag/github-pr-coverage-status-1.6.0)

- Cobertura plugin ignores zero for ```lineRate``` or ```branchRate``` as result ```lineRate=0 branchRate=0.5 => Coverage 25%``` became ```0.5``` as ```lineRate=0```, same policy for ```branchRate```
- Log Enchacements

### 1.5.0

Supporting Master Coverage from SonarQube. Check (details)[#master-coverage-from-sonar]

### 1.4.0

Supporting of private Jenkins with public GitHub. To enable that mode goto Jenkins Configuration and turn on ```Jenkins is not accessible for GitHub```

### 1.3.0

Add support SimpleCov JSON coverage report for Ruby

### 1.2.0

Add alternative text to coverage image for case when GitHub doesn't able to show image

### 1.1.1

Correct parsing Cobertura report for edge cases 

### 1.0.8

First public release to Jenkins Plugin Repo
