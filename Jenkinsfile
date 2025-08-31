pipeline {
  agent any
  environment {
      DIRECTORY_PATH = 'TBD'
      TESTING_ENVIRONMENT = 'NA'
      PRODUCTION_ENVIRONMENT= 'Alfie'
  }
  
  stages {
      stage('Build') {
          steps {
              echo "Fetch the source code from the directory path specified by the environment variable: ${env.DIRECTORY_PATH}"
              echo "Compile the code and generate any necessary artefacts."
          }
      }
  stage('Test') {
      steps {
          echo "Unit Tests."
          echo "Integration Tests."
      }
  }
  
  stage('Code') {
      steps {
          echo "Check the quality of the code."
      }   
  }
  
  stage('Deploy') {
      steps {
          echo "Deploy the application to a testing environment: ${env.TESTING_ENVIRONMENT}"
      }
  }
  
  stage('Approval') {
      steps {
          sleep time: 10, unit: 'SECONDS'
          echo "Approved."
      }
  }
  
  stage('Deploy to Production') {
      steps {
          echo "Code has been deployed to Production Environment. (${env.PRODUCTION_ENVIRONMENT})"
      }
    }
  }
}
