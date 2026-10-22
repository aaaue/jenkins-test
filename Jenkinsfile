pipeline {
    agent any
    tools {
        maven 'Maven-3.9.6'   // Manage Jenkins > Tools 에서 등록한 Maven 이름
    }
    stages {
        stage('build') {
            steps {
                sh 'mvn --version'
            }
        }
    }
}
