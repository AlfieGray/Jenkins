pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        echo 'Building project with Maven...'
      }
    }

    stage('Unit and Integration Tests') {
      steps {
        echo 'Running tests with JUnit and Maven Surefire/Failsafe...'
      }
    }

    stage('Code Analysis') {
      steps {
        echo 'Analysing code quality with SonarQube...'
      }
    }

    stage('Security Scan') {
      steps {
        echo 'Scanning for vulnerabilities with OWASP Dependency-Check...'
      }
    }

    stage('Deploy to Staging') {
      steps {
        echo 'Deploying application to AWS EC2 Staging environment...'
      }
    }

    stage('Integration Tests on Staging') {
      steps {
        echo 'Running integration tests against Staging environment...'
      }
    }

    stage('Deploy to Production') {
      steps {
        echo 'Deploying application to AWS EC2 Production environment...'
      }
    }
  }
}
