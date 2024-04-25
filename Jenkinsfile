pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Preparation') {
            steps {
                deleteDir()
            }
        }
        stage('SCM') {
            steps {
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonarqube', installationName: 'sonarqube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Build') {
            steps {
                // Run Maven on a Unix agent.
                sh 'mvn -Dmaven.test.failure.ignore=true package'
            }

            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
