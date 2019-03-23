def getComitter() {
    return sh(script: 'git show -s --pretty=%an', returnStdout: true)
}

def getComitterEmail() {
    return sh(script: 'git show -s --pretty=%ae', returnStdout: true)
}

def getBranch() {
    return sh(script: 'git show -s --pretty=%ae', returnStdout: true)
}

pipeline {
    agent none
    options {
        skipDefaultCheckout(true)
        checkoutToSubdirectory('trainee')
    }
    stages {
        stage('Checkout') {
            agent any
            steps {
                sh 'pwd'
                dir('trainee-code') {
                    checkout scm
                    sh 'pwd'
                    sh 'ls -al'
                }
                dir('test-code') {
                    git(branch: 'master',
                            url: 'https://github.com/somallg/test-pipeline-js.git')
                    sh 'pwd'
                    sh 'ls -al'
                }
            }
        }
        stage('Merge Code') {
            agent any
            steps {
                echo 'Merge Trainee Code and Test Code'
                sh 'pwd'
                sh 'ls -al'
            }
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
