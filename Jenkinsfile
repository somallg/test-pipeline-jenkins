def getComitter() {
    return sh(script: 'git show -s --pretty=%an', returnStdout: true)
}

def getComitterEmail() {
    return sh(script: 'git show -s --pretty=%ae', returnStdout: true)
}

def getBranch() {
    return sh(script: 'git show -s --pretty=%ae', returnStdout: true)
}

def traineeCode = 'trainee-code'

def testCode = 'test-code'

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
                dir(traineeCode) {
                    checkout scm
                    sh 'pwd'
                    sh 'ls -al'
                }
                dir(testCode) {
                    git(branch: 'master',
                            url: 'https://github.com/somallg/test-pipeline-js.git')
                    sh 'pwd'
                    sh 'ls -al'
                }
            }
        }
        stage('Merge Code') {
            agent {
                docker 'instrumentisto/rsync-ssh'
            }
            steps {
                echo 'Merge Trainee Code and Test Code'
                sh 'rsync -a test-code/ trainee-code/'
                sh 'ls -alR trainee-code'
            }
        }
        stage('Move code to workspace') {
            agent any
            steps {
                sh 'find ${traineeCode} -maxdepth 1 -exec mv -t . {} +'
                sh 'rm -rf ${traineeCode}'
                sh 'rm -rf ${testCode}'
                sh 'ls -alR'
            }
        }
        stage('Install Dependencies') {
            agent {
                docker 'node:10-alpine'
            }
            steps {
                sh 'pwd'
                echo 'Install dependencies'
                sh 'npm install'
                sh 'pwd'
                sh 'ls -al'
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
                sh 'ls -al'
            }
            post {
                always {
                    junit 'junit.xml'
                }
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
