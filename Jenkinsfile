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
                sh 'rsync -a test-code/ trainee-code/'
                sh 'ls -alR trainee-code'
            }
        }
        stage('Install Dependencies') {
            agent {
                docker 'node:10-alpine'
            }
            steps {
                echo 'Install dependencies'
                sh 'npm ci'
            }
        }
        stage('Build') {
            agent any
            steps {
                echo 'Build code'
            }
        }
        stage('Test') {
            agent {
                docker 'node:10-alpine'
            }
            steps {
                echo 'Run unit test'
                sh 'npm test'
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
