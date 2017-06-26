#!/usr/bin/env groovy
// Jenkinsfile for implementing build pipeline for java-example project. This pipeline requires following from Jenkins master:
// Plugins:
//   - pipeline plugins bundle
//   - gitlab-plugin
//   - git-plugin
// Global tools:
//   - maven with name 'Maven 3.3.9'
//   - sonar-scanner with name 'Sonar-scanner 2.8'
// Build nodes:
//   - java-8-node.
// Credentials:
//   - gitlab-login: for cloning the repo from Gitlab.
// Others:
//   - Gitlab connection configured so can push pipeline status
String GIT_REPO = 'git@git.test.com:devops/java-example.git'
String GIT_LOGIN = 'gitlab-login'
String COMMIT = ''
String SHORT_COMMIT = ''
String BRANCH = ''
Boolean MULTIBRANCH = true

if (MULTIBRANCH) {
  // To be able to push status to gitlab. However, messes up standard pipeline by removing part of job configuration. Requires job re-configuration and Jenkins restart to recover!
  properties([[$class: 'GitLabConnectionProperty', gitLabConnection: 'gitlab']])
  BRANCH = env.BRANCH_NAME
} else {
  BRANCH = env.gitlabSourceBranch
}

gitlabCommitStatus("Build") {

  node('java-8-node') {

    stage('SCM') {
      // Checkout the commit that triggered the build
      checkout scm

      // Save commit hash to env variable so can use it later for tagging
      COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
      SHORT_COMMIT = COMMIT.take(6)
      echo """
      COMMIT = ${COMMIT}
      BRANCH = ${BRANCH}
      JOB_NAME = ${env.JOB_NAME}
      JOB_BASE_NAME = ${env.JOB_BASE_NAME}
      """
      assert BRANCH
    }

    stage('Unit Test') {
      // Build and run tests
      def mvnHome = tool 'Maven 3.3.9'
      sh "${mvnHome}/bin/mvn clean install"
    }

    stage('Analyze') {
      // Run SonarQube analysis
      def scannerHome = tool 'Sonar-scanner 2.8';
      withSonarQubeEnv('sonarqube.test.com') {
        sh "${scannerHome}/bin/sonar-scanner"
      }
    }
  }

}
