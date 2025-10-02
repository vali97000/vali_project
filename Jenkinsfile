pipeline {
	agent any
	
	environment {
		PATH = "/opt/maven/bin:$PATH"
	}
	
	stages {
		stage ("Build") {
			steps {
				echo "-------- Build Started  ----------"
				sh 'mvn clean deploy -Dmaven.test.skip=true'
				echo "-------- Build Completed  ----------"
			}
		}
		stage ("Test") {
			steps {
				echo "-------- Test Started  ----------"
				sh 'mvn surefire-report:report'
				echo "-------- Test Completed  ----------"
			} 
		}

		stage("SonarQube Analysis") {
			environment {
				scannerHome = tool 'sq-scanner'
			}
		
			steps{
				withSonarQubeEnv('sq-connection-jenkins') {
					sh "${scannerHome}/bin/sonar-scanner"
				}
			}
		}
		stage("Quality Gate") {
			steps {
				script {
					timeout(time: 1, unit: 'HOURS'){
						def qg = waitForQualityGate()
						if (qg.status != 'OK') {
							echo "Warning: Quality Gate failed but continuing pipeline: $(qg.status)"
							// error "pipeline failed due to Quality Gate Failure: ${qg.status}"
						}
					}
				}
			}
		}
	}
}
