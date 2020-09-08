pipeline {
  agent {
    node {
      label 'master'
    }

  }
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean compile'
      }
    }

    stage('parallel execution') {
      parallel {
        stage('Sonar') {
          agent {
            node {
              label 'master'
            }

          }
          environment {
            scannerHome = 'SonarScanner'
          }
          steps {
            withSonarQubeEnv('SonarLocal') {
              bat "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=DemoPrj -Dsonar.projectName=DemoPrj -Dsonar.sources=src -Dsonar.java.binaries=target"
            }

            timeout(time: 1, unit: 'MINUTES') {
              script {
                def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
              }

            }

          }
        }

        stage('SeleniumTest') {
          agent {
            node {
              label 'Slave1'
            }

          }
          steps {
            bat 'mvn test'
          }
        }

      }
    }

  }
  post {
    success {
      publishHTML(allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'surefire-reports', reportFiles: 'index.html', reportName: 'Test Report')
    }

  }
}