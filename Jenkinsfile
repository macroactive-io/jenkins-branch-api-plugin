properties([
  pipelineTriggers([githubPush()]),
  disableConcurrentBuilds(abortPrevious: true),
  buildDiscarder(
    logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '',
      daysToKeepStr: '', numToKeepStr: '20')),
])

String label = "maven-${UUID.randomUUID().toString()}" // TODO use POD_LABEL when available
String mvnOpts = '-DskipTests -ntp -Darguments="-DskipTests -ntp"'

// for cache see kubernetes-plugin.git/examples/maven-with-cache.groovy

podTemplate(
  label: label,
  containers: [
    containerTemplate(
      name: 'maven',
      image: 'maven:3.9.10-eclipse-temurin-24-alpine',
      ttyEnabled: true, command: 'cat',
    )
  ],
  volumes: [
    emptyDirVolume(mountPath: '/root/.m2/repository')
  ]
) {

  node(label) {
    stage('Checkout') {
      checkout scm
    }

    /* stage('Release prepare') {
      container('maven') {
          sh "mvn -B ${mvnOpts} release:clean release:prepare"
      }
    } */

    stage('Build') {
      container('maven') {
        sh "mvn -B ${mvnOpts} install"
      }
    }

    stage('Artifacts') {
      // https://www.jenkins.io/doc/pipeline/steps/core/#code-archiveartifacts-code-archive-the-artifacts
      archiveArtifacts artifacts: 'target/branch-api.hpi',
        fingerprint: true, onlyIfSuccessful: true
    }
  }
}

