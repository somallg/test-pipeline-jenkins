pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker 'maven:3-alpine'
            }
            steps {
                echo 'Hello, Maven'
                sh 'mvn --version'
                sh 'pwd'
            }
        }
        stage('Test') {
            agent {
                docker 'openjdk:8-jre'
            }
            steps {
                echo 'Hello, JDK'
                sh 'java -version'
            }
        }
        stage('Test Git') {
            agent any
            steps {
                sh "echo ${env.GIT_AUTHOR_NAME}"
                sh "echo ${env.GIT_AUTHOR_EMAIL}"
            }
        }
    }
}
