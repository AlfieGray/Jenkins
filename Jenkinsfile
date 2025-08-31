pipeline {
  agent any

  environment {
    // App & hosts
    APP_NAME        = 'myapp'
    STAGING_HOST    = 'ec2-user@staging-ec2.example.com'
    PROD_HOST       = 'ec2-user@prod-ec2.example.com'
    REMOTE_DIR      = "/opt/${APP_NAME}"
    JAR_NAME        = "${APP_NAME}.jar"                  // produced by Maven
    SERVICE_NAME    = "${APP_NAME}.service"              // systemd unit on EC2
    // SonarQube
    SONARQUBE_ENV   = 'sonarqube-main'
  }

  options {
    timestamps()
    skipDefaultCheckout(true)
    ansiColor('xterm')
  }

  tools {
    jdk 'jdk17'
    maven 'maven-3.9'
  }

  stages {
    stage('Checkout') {
      steps {
        // If repo is private, add 'credentialsId: "github-creds"'
        checkout([$class: 'GitSCM',
          branches: [[name: '*/main']],
          userRemoteConfigs: [[url: 'https://github.com/<owner>/<repo>.git']]
        ])
      }
    }

    stage('Build') {
      steps {
        sh '''
          mvn -B -ntp -DskipTests package
        '''
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }

    stage('Unit & Integration Tests') {
      steps {
        sh '''
          # Unit tests (Surefire)
          mvn -B -ntp test
          # Integration tests (Failsafe) - expects tests named *IT.java
          mvn -B -ntp -Pint-test failsafe:integration-test failsafe:verify
        '''
      }
      post {
        always {
          junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml, **/failsafe-reports/*.xml'
        }
      }
    }

    stage('Code Analysis (SonarQube)') {
      steps {
        withSonarQubeEnv("${SONARQUBE_ENV}") {
          sh '''
            mvn -B -ntp sonar:sonar \
              -Dsonar.projectKey=myapp \
              -Dsonar.projectName=myapp
          '''
        }
      }
    }

    stage('Security Scan (OWASP Dependency-Check)') {
      steps {
        sh '''
          # Generates reports in "target/dependency-check-report.*"
          mvn -B -ntp org.owasp:dependency-check-maven:check \
             -Dformat=ALL \
             -DfailBuildOnCVSS=7
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'target/dependency-check-report.*', allowEmptyArchive: true
        }
      }
    }

    stage('Deploy to Staging') {
      when { branch 'main' }
      steps {
        sshagent(credentials: ['ec2-staging-ssh']) {
          sh '''
            # copy artifact
            scp -o StrictHostKeyChecking=no target/*.jar ${STAGING_HOST}:${REMOTE_DIR}/${JAR_NAME}
            # restart service
            ssh -o StrictHostKeyChecking=no ${STAGING_HOST} "sudo systemctl restart ${SERVICE_NAME} && sudo systemctl is-active ${SERVICE_NAME}"
          '''
        }
      }
    }

    stage('Integration Tests on Staging') {
      steps {
        // Replace this with Newman/REST Assured/etc. if you have real suites
        sh '''
          set -e
          # simple smoke checks
          curl -fsS http://staging.example.com/actuator/health
          curl -fsS -o /dev/null -w "%{http_code}\\n" http://staging.example.com/ | grep -E "200|302"
        '''
      }
    }

    stage('Deploy to Production') {
      when { branch 'main' }
      steps {
        input message: 'Promote to PRODUCTION?', ok: 'Deploy'
        sshagent(credentials: ['ec2-prod-ssh']) {
          sh '''
            scp -o StrictHostKeyChecking=no target/*.jar ${PROD_HOST}:${REMOTE_DIR}/${JAR_NAME}
            ssh -o StrictHostKeyChecking=no ${PROD_HOST} "sudo systemctl restart ${SERVICE_NAME} && sudo systemctl is-active ${SERVICE_NAME}"
          '''
        }
      }
    }
  }

  post {
    success {
      echo "Pipeline complete ✅"
    }
    failure {
      echo "Pipeline failed ❌"
    }
    always {
      // keep key build outputs around
      archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
    }
  }
}
