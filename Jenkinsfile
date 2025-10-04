pipeline {
	agent any
	environment {
		PATH = "/opt/maven/bin:$PATH"
	}
	stages {
		stage('Build') {
			steps {
				sh 'mvn clean package'
			}
		}
		stage('sonarQube Analysis') {
			environment {
				scannerHome = tool 'sq-scanner'
			}
			steps {
				withSonarQubeEnv('sq-connection-jenkins') {
					sh '${scannerHome}/bin/sonar-scanner'
				}
			}
		}
	}
}
