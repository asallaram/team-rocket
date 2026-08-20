pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Code Coverage') {
            steps {
                dir('starter') {
                    sh 'mvn -B clean verify'
                }
                timeout(time: 20, unit: 'SECONDS') {
                    input message: 'Approve archiving the code coverage artifacts?', ok: 'Proceed'
                }
                archiveArtifacts artifacts: 'starter/target/site/jacoco/**', allowEmptyArchive: false
            }
        }
        stage('Build Image') {
            steps {
                dir('starter') {
                    sh 'mvn -B clean package -DskipTests'
                    sh 'docker build -t team-skeleton:latest .'
                }
            }
        }
        stage('Smoke Test') {
            steps {
                dir('starter') {
                    sh 'docker run --rm team-skeleton:latest'
                }
            }
        }
    }
}