pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {
        stage("build") {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'sq-schanner'
            }

            steps {
                withSonarQubeEnv('sq-connection-jenkins') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}

