pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean compile'
      }
    }
	parallel {
	stage('Sonar') {
		environment {
        scannerHome = tool 'SonarScanner'
		}
	  steps {
        withSonarQubeEnv('SonarLocal') {
            bat "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=DemoPrj -Dsonar.projectName=DemoPrj -Dsonar.sources=src -Dsonar.java.binaries=target"
        }
        timeout(time: 1, unit: 'MINUTES') {
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
        bat 'mvn test'
      }
	}
  }
  }
}
