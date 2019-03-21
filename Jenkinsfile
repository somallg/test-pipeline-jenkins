def getComitter() {
    return sh(script: 'git show -s --pretty=%an', returnStdout: true)
}

def getComitterEmail() {
    return sh(script: 'git show -s --pretty=%ae', returnStdout: true)
}

pipeline {
    agent none
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Checkout') {
            agent any
            checkout scm
        }
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
                sh "echo Trainee Name: ${getComitter()}"
                sh "echo Trainee Email: ${getComitterEmail()}"
            }
        }
    }
}
