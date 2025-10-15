pipeline {
  agent any

  tools {
    maven "Maven_3_9"
  }

  options {
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build & Test') {
      steps {
        bat 'mvn -B -e -DskipTests=false clean package'
      }
      post {
        always {
          junit 'target\\surefire-reports\\*.xml'
          archiveArtifacts artifacts: 'target\\*.jar', fingerprint: true
        }
      }
    }

    stage('Run App (demo)') {
  steps {
    bat '''
      for %%f in (target\\*.jar) do (
        echo Running %%f
        java -jar "%%f"
      )
    '''
  }
    }
  }
}
