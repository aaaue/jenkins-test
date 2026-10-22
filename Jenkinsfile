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
        stage('hello'){
            steps{
                echo 'Hello World!'
            }
        }
    }
}
