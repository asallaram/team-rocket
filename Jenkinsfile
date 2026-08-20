pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                dir('starter') {
                    sh 'mvn -B clean package -DskipTests'
                }
            }
        }

        stage('Validation') {
            parallel {
                stage('Code Coverage') {
                    steps {
                        dir('starter') {
                            sh 'mvn -B clean verify'
                        }
                        archiveArtifacts artifacts: 'starter/target/site/jacoco/**', allowEmptyArchive: false
                    }
                }

                stage('Code Quality') {
                    steps {
                        dir('starter') {
                            sh 'mvn -B checkstyle:checkstyle'
                        }
                        archiveArtifacts artifacts: 'starter/target/checkstyle-result.xml, starter/target/reports/checkstyle.html', allowEmptyArchive: false
                    }
                }
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