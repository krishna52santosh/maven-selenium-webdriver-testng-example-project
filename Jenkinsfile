pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean install'
      }
    }
	stage('Sonar') {
		environment {
        scannerHome = tool 'SonarScanner'
		}
	  steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=DemoPrj -Dsonar.projectName=DemoPrj -Dsonar.sources=src"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
	  }
	  

    }
  }
}
