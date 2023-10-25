node {
  def myGradleContainer = docker.image('gradle:jdk8-alpine')
  myGradleContainer.pull()
  stage('SCM check y Preparacion') {
    checkout scm
  }
  stage('Testeo') {
     myGradleContainer.inside("-v ${env.HOME}/.gradle:/home/.gradle") {
       sh 'cd complete && gradle test'
     }
  }
  stage('Ejecucion') {
     myGradleContainer.inside("-v ${env.HOME}/.gradle:/home/.gradle") {
       sh 'cd complete && gradle run'
     }
  }
}
