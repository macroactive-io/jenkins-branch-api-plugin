def label = "maven-${UUID.randomUUID().toString()}" // TODO use POD_LABEL when available
def mvnOpts = '-DskipTests -ntp -Darguments="-DskipTests -ntp"'

// for cache see kubernetes-plugin.git/examples/maven-with-cache.groovy

podTemplate(label: label, containers: [
  containerTemplate(name: 'maven', image: 'maven:3.9.10-eclipse-temurin-11-alpine', ttyEnabled: true, command: 'cat')
  ], volumes: [
  emptyDirVolume(mountPath: '/root/.m2/repository')
  ]) {

  node(label) {
    stage('Checkout') {
      checkout scm
    }
    /* stage('Release prepare') {
      container('maven') {
          sh "mvn -B ${mvnOpts} release:clean release:prepare"
      }
    } */
    stage('Release perform') {
      container('maven') {
          sh "mvn -B ${mvnOpts} install"
      }

      // https://www.jenkins.io/doc/pipeline/steps/core/#code-archiveartifacts-code-archive-the-artifacts
      archiveArtifacts artifacts: 'target/multi-branch-priority-sorter.hpi',
        fingerprint: true, onlyIfSuccessful: true
    }
  }
}

