pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean compile'
      }
    }
	stage('parallel execution'){
	parallel {
	stage('Sonar') {
		environment {
        scannerHome = tool 'SonarScanner'
		}
	  steps {
        withSonarQubeEnv('SonarLocal') {
            bat "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=DemoPrj -Dsonar.projectName=DemoPrj -Dsonar.sources=src -Dsonar.java.binaries=target"
        }
        timeout(time: 10, unit: 'SECONDS') {
            //waitForQualityGate abortPipeline: true
			script {
                    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
        }
	  }
	  

    }
	stage('SeleniumTest'){
	  steps {
		//bat 'TIMEOUT 10' 
        bat 'mvn test'
		bat '${WORKSPACE}\target\surefire-reports\index.html ${WORKSPACE}\target'
		  
      }
	  
	}
  }
  }
  
   
  }	
  post {
        success {
          // publish html
          publishHTML target: [
              allowMissing: false,
              alwaysLinkToLastBuild: false,
              keepAll: true,
              reportDir: 'target',
              reportFiles: 'index.html',
              reportName: 'Test Report'
            ]
        }
      }
	
}
