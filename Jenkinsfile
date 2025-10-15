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
        powershell '''
          $log = "app.log"
          # Start Java process and capture PID safely
          $proc = Start-Process -FilePath "java" -ArgumentList "-cp","target\\classes","com.example.App" -RedirectStandardOutput $log -RedirectStandardError $log -PassThru -WindowStyle Hidden
          Write-Host "Started App with PID $($proc.Id)"
          Start-Sleep -Seconds 5
          # Stop just this process; don't nuke all java.exe instances
          Stop-Process -Id $proc.Id -Force
          Write-Host "Stopped App"
        '''
      }
    }
  }
}
