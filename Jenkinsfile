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
					timeout(time: 1, unit: 'MINUTES'){
						def qg = waitForQualityGate()
						if (qg.status != 'OK') {
							echo "Warning: Quality Gate failed but continuing pipeline: ${qg.status}"
						}
					}
				}
			}
		}
		stage("JFrog Artifactory") {
			steps {
				script {
					echo '<============JAR Publish Started===============>'
					def server = Artifactory.newServer url: "${registry}/artifactory", credentialId: "JFrog-Token"
					def properties = "buildid = ${env.BUILD_ID}, commitid= ${GIT_COMMIT}"
					def uploadSpec = """{
						"files": [
							{
								"pattern": "jarstaging/(*)",
								"target": "vali-libs-release-local/{1}",
								"flat": "false",
								"props": "${properties}",
								"exclusions": ["*.sha1", "*.md5"]
								
							}
						]
					}"""
					def buildinfo = server.upload(uploadSpec)
					buildinfo.env.collect()
					server.publishBuildInfo(buildinfo)
					echo '<==========Jar Publish Ended=============>'
				}
			}
		}
	}
}
