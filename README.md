# Description


This project is part of examples that shows the DevOps tool chain for a Java project. It shows:
* Jenkinsfile for CI pipeline that runs:
  * Tests and coverage analysis,
  * Sonarqube analysis.
* Running the Jenkins pipeline in a container node.


# Build pipeline

See the Jenkinsfile for details.


# Jenkins build node

This project uses generic containerized Java build node: dev-docker-registry.test.com/common/jenkins-node-java:jdk-8
