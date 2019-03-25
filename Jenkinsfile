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
    environment {
        traineeName = ''
        traineeEmail = ''
    }
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
        stage('Get Trainee Information') {
            agent any
            steps {
                dir(traineeCode) {
                    script {
                        traineeName = getComitter()
                        traineeEmail = getComitterEmail()
                    }
                }
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
                    sh 'npm test || true'
                    sh 'ls -al'
                }
            }
            post {
                always {
                    dir(traineeCode) {
                        sh 'ls -al'
                        sh 'cat junit.xml'
                        junit 'junit.xml'
                        emailext(body: '''${JELLY_SCRIPT, template="report-email-jelly.html"}''',
                                subject: "[Fresher Academy] Your work report",
                                to: "tq.duong@icloud.com",
                                replyTo: "tq.duong@icloud.com",
                                recipientProviders: [[$class: 'CulpritsRecipientProvider']])
                    }
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
