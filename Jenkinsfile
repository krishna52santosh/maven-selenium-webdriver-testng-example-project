pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean compile'
      }
    }
	stage('Sonar') {
		environment {
        scannerHome = tool 'SonarScanner'
		}
	  steps {
        withSonarQubeEnv('SonarLocal') {
            bat "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=DemoPrj -Dsonar.projectName=DemoPrj -Dsonar.sources=src -Dsonar.java.binaries=target"
        }
        timeout(time: 1, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
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
