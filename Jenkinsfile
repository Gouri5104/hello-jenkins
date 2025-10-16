pipeline {
  agent any

  tools {
    maven "Maven_3_9"

    jdk 'JDK17'  // Must match exactly the name in Jenkins JDK config
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
    // Pick the configured JDK tool name as shown in Manage Jenkins → Tools
    tool name: 'JDK17', type: 'jdk'  // <-- change 'JDK17' to your real name
    withEnv(["PATH+JAVA=${tool name: 'JDK17', type: 'jdk'}/bin"]) {

      bat '''
        echo ===== JAVA DIAGNOSTICS =====
        where java
        java -version

        echo ===== LIST BUILT JARS =====
        dir /b target\\*.jar

        echo ===== RUN (with crash logs enabled) =====
        set JAVA_TOOL_OPTIONS=-XX:+CreateMinidumpOnCrash -XX:ErrorFile=hs_err_pid%p.log
        for %%f in (target\\*.jar) do (
          echo Running %%f
          java -Xlog:os -Xint -Djava.awt.headless=true -jar "%%f"
        )
        echo ===== EXIT CODE !ERRORLEVEL! =====
      '''
    }
  }
  post {
    always {
      archiveArtifacts artifacts: 'hs_err_pid*.log,*.mdmp,**/hs_err_pid*.log', allowEmptyArchive: true
    }
  }
}

  }
}
