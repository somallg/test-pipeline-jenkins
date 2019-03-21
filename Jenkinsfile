pipeline {
    agent none
    options {
        skipDefaultCheckout()
    }
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
            changeAuthors = currentBuild.changeSets.collect { set ->
                set.collect { entry -> entry.author.fullName }
            }.flatten()
            steps {
                sh "echo ${changeAuthors}"
            }
        }
    }
}
