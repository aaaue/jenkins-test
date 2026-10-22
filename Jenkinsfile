pipeline {
    agent any
    tools {
        jdk 'JDK21'
    }
    stages {
        stage('build') {
            steps {
                sh 'mvn --version'
            }
        }
    }
}
