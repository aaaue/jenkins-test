pipeline {
    agent any

    tools {
        jdk 'JDK21'          // Manage Jenkins > Tools 에 등록한 이름과 동일해야 함
    }

    environment {
        TOMCAT_HOME  = '/opt/tomcat'      // 실제 경로로 수정
        TOMCAT_SVC   = 'tomcat'           // 실제 서비스 이름으로 수정
        APP_NAME     = 'myapp'
        BACKUP_DIR   = '/var/lib/jenkins/backup'
        HEALTH_URL   = 'http://localhost:8080/myapp/health.txt'
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '30'))
        timeout(time: 30, unit: 'MINUTES')
    }

    stages {
        stage('Build & Test') {
            steps {
                sh 'mvn -B clean package'
            }
            post {
                always { junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml' }
            }
        }

        stage('Archive') {
            steps { archiveArtifacts artifacts: "target/${APP_NAME}.war", fingerprint: true }
        }

        stage('Deploy to DEV') {
            when { branch 'main' }
            steps {
                sh '''
                  set -e
                  mkdir -p "$BACKUP_DIR"
                  TS=$(date +%Y%m%d_%H%M%S)
                  cp -p "$TOMCAT_HOME/webapps/$APP_NAME.war" "$BACKUP_DIR/$APP_NAME.war.$TS" || true
                  sudo systemctl stop "$TOMCAT_SVC"
                  rm -rf "$TOMCAT_HOME/webapps/$APP_NAME" "$TOMCAT_HOME/webapps/$APP_NAME.war"
                  cp "target/$APP_NAME.war" "$TOMCAT_HOME/webapps/"
                  sudo systemctl start "$TOMCAT_SVC"
                '''
            }
        }

        stage('Health Check') {
            when { branch 'main' }
            steps {
                retry(10) {
                    sleep 10
                    sh 'curl -fsS -o /dev/null "$HEALTH_URL"'
                }
            }
        }
    }

    post {
        success { echo "성공: ${env.JOB_NAME} #${env.BUILD_NUMBER}" }
        failure { echo "실패: ${env.BUILD_URL}console 확인" }
    }
}
