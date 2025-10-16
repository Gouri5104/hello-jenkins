pipeline {
    agent any

    // Define tools
    tools {
        jdk 'JDK17'        // Must match the JDK name configured in Jenkins Global Tool Configuration
        maven 'Maven'     // Optional: define Maven tool if installed in Jenkins
    }

    environment {
        // Force headless mode to prevent GUI issues in Jenkins agent
        JAVA_TOOL_OPTIONS = "-Djava.awt.headless=true"
    }

    stages {

        stage('Checkout') {
            steps {
                // Checkout from Git
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Gouri5104/hello-jenkins.git',
                        credentialsId: 'git-cred'
                    ]]
                ])
            }
        }

        stage('Build & Test') {
            steps {
                // Maven clean, compile, package, run tests
                bat 'mvn clean package -DskipTests=false'
            }

            post {
                // Archive test results for Jenkins reporting
                always {
                    junit 'target/surefire-reports/*.xml'
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }

        stage('Run App') {
            steps {
                // Run the built JAR safely
                bat """
                echo ===== JAVA DIAGNOSTICS =====
                java -version

                echo ===== RUNNING JAR =====
                for %%f in (target\\*.jar) do (
                    echo Running %%f
                    java -Xint -Djava.awt.headless=true -jar "%%f"
                )
                """
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for errors.'
        }
    }
}
