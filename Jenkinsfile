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
        stage('Deploy') {
            steps {
                sh 'ansible-playbook -i inventory.ini --private-key=$ANSIBLE_PRIVATE_KEY playbook.yml'
            }
        }
    }
}
