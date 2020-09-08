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
		bat 'xcopy C:\Softwares\Slave1\workspace\er-testng-example-project_master\target\surefire-reports\index.html C:\Softwares\Slave1\workspace\er-testng-example-project_master\target\index.html'
		  
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
