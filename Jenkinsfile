pipeline {
    agent any

    environment {
        ANSIBLE_PRIVATE_KEY = credentials('ansible-private-key')
    }

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

        stage('Compile') {
            steps {
                sh 'mvn clean'
                sh 'mvn compile'
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
                sh 'mvn -Dmaven.test.failure.ignore=true package'
            }
            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }

        stage('Deploy') {
            steps {
                ansiblePlaybook(
                    installation: 'ansible',
                    inventory: 'inventory.ini',
                    playbook: 'playbook.yml',
                    extras: '--private-key ${ANSIBLE_PRIVATE_KEY}',
                    vaultTmpPath: '',
                    disableHostKeyChecking: true
                )
            }
        }
    }
}
