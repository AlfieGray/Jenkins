pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        echo 'Building project with Maven...'
        // Example (uncomment later):
        // sh 'mvn -B -ntp clean package'
      }
    }

    stage('Unit and Integration Tests') {
      steps {
        echo 'Running tests with JUnit (Surefire/Failsafe)...'
        // Example (uncomment later):
        // sh 'mvn -B -ntp test'
        // sh 'mvn -B -ntp failsafe:integration-test failsafe:verify'
      }
    }

    stage('Code Analysis') {
      steps {
        echo 'Analyzing code quality with SonarQube...'
        // Example (uncomment later, after SonarQube server is added in Jenkins):
        // withSonarQubeEnv('sonarqube-main') {
        //   sh 'mvn sonar:sonar -Dsonar.projectKey=myapp'
        // }
      }
    }

    stage('Security Scan') {
      steps {
        echo 'Scanning for vulnerabilities with OWASP Dependency-Check...'
        echo 'This is a test!!'
        // Example (uncomment later):
        // sh 'mvn org.owasp:dependency-check-maven:check'
      }
    }

    stage('Deploy to Staging') {
      steps {
        echo 'Deploying application to AWS EC2 (Staging)...'
        // Example (uncomment later):
        // sshagent(['ec2-staging-ssh']) {
        //   sh 'scp target/myapp.jar ec2-user@staging:/opt/myapp/'
        //   sh 'ssh ec2-user@staging "sudo systemctl restart myapp.service"'
        // }
      }
    }

    stage('Integration Tests on Staging') {
      steps {
        echo 'Running integration tests against Staging environment...'
        // Example (uncomment later):
        // sh 'curl -f http://staging.example.com/health'
      }
    }

    stage('Deploy to Production') {
      steps {
        echo 'Deploying application to AWS EC2 (Production)...'
        // Example (uncomment later, with approval gate):
        // input message: 'Deploy to PRODUCTION?', ok: 'Yes, Deploy'
        // sshagent(['ec2-prod-ssh']) {
        //   sh 'scp target/myapp.jar ec2-user@prod:/opt/myapp/'
        //   sh 'ssh ec2-user@prod "sudo systemctl restart myapp.service"'
        // }
      }
    }
  }
}
