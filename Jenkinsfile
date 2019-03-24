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
                sh "rsync -a ${testCode}/ ${traineeCode}/"
                sh "ls -alR ${traineeCode}"
                sh "pwd"
                sh "ls -al"
            }
        }
        stage('Install Dependencies') {
            agent {
                docker 'node:10-alpine'
            }
            steps {
                dir(traineeCode) {
                    sh 'pwd'
                    echo 'Install dependencies'
                    sh 'pwd'
                    sh 'ls -al'
                }
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
                dir(traineeCode) {
                    echo 'Run unit test'
                    sh 'npm test'
                    sh 'ls -al'
                }
            }
            post {
                always {
                    dir(traineeCode) {
                        sh 'junit.xml'
                        junit 'junit.xml'
                        emailext(body: '''${JELLY_SCRIPT, template="report-email-jelly.html"}''',
                                subject: "[Fresher Academy] Your work report",
                                to: "${getComitterEmail()}",
                                replyTo: "${getComitterEmail()}",
                                recipientProviders: [[$class: 'CulpritsRecipientProvider']])
                    }
                }
            }
        }
        stage('Test Git') {
            agent any
            steps {
                dir(traineeCode) {
                    sh 'pwd'
                    sh "echo Trainee Name: ${getComitter()}"
                    sh "echo Trainee Email: ${getComitterEmail()}"
                    sh "echo Branch: ${getBranch()}"
                }
            }
        }
        stage('Clean up') {
            agent any
            steps {
                echo 'Clean up'
            }
        }
    }
}
