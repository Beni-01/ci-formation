pipeline {
  agent any
  tools { maven 'Maven' }
  triggers { pollSCM('H/2 * * * *') }
  environment { NEXUS_URL = 'http://nexus:8081' }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build & Tests') {
      steps {
        // TODO A : construire et tester en mode batch
        sh 'mvn -B clean verify'
      }
    }
    stage('Publish') {
      steps {
        // TODO B : injecter le credential 'nexus-deploy'
        //          dans NEXUS_USER / NEXUS_PASSWORD, puis publier
        //          avec ci-settings.xml vers $NEXUS_URL
        withCredentials([usernamePassword(
          credentialsId: 'nexus-deploy',
          usernameVariable: 'NEXUS_USER',
          passwordVariable: 'NEXUS_PASSWORD'
        )]) {
          sh 'mvn -B -s ci-settings.xml deploy -DskipTests -Dnexus.url=$NEXUS_URL'
        }
      }
    }
  }

  post {
    // TODO C : toujours publier les rapports de tests
    always {
      junit '**/target/surefire-reports/*.xml,**/target/failsafe-reports/*.xml'
    }
    // TODO D : en cas de succès, archiver le JAR
    success {
      archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
    }
  }
}