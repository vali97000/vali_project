pipeline {
	agent any

    	environment {
        	PATH = "/opt/maven/bin:$PATH"
    	}

    	stages {
        	stage('Build') {
            		steps {
                		echo "-------- Build Started ----------"
                		sh 'mvn clean deploy -Dmaven.test.skip=true'
                		echo "-------- Build Completed ----------"
            		}
        	}

        	stage('Test') {
            		steps {
                		echo "-------- Test Started ----------"
               			sh 'mvn surefire-report:report'
                		echo "-------- Test Completed ----------"
            		}
        	}

    	}
}
