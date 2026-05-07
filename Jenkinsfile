pipeline {
    agent any

    tools {
        maven 'Maven-3.9.15'
    }

    environment {
        PATH = "/opt/maven3/bin:${env.PATH}"
    }

    stages {

        stage('SCM Code Clone') {
            steps {
                git branch: 'main',
                    credentialsId: 'GithHub',
                    url: 'https://github.com/Karthiknarayana26/batch1.git'
            }
        }

        stage('Code Review') {
            steps {
                sh "mvn clean sonar:sonar"
            }
        }

        stage('Maven Build') {
            steps {
                sh "mvn package"
            }
        }

        stage('Artifact Upload') {
            steps {
                nexusArtifactUploader(
                    artifacts: [
                        [
                            artifactId: 'webappliaction',
                            classifier: '',
                            file: 'target/webappliaction.war',
                            type: 'war'
                        ]
                    ],
                    credentialsId: 'nexus',
                    groupId: 'com.batch1',
                    nexusUrl: '3.14.10.183:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'maven-snapshots',
                    version: '1.0-SNAPSHOT'
                )
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sshagent(['3504bec8-1207-4f2f-9660-713e6bb81041']) {
                    sh """
                        scp -o StrictHostKeyChecking=no \
                        target/webappliaction.war \
                        ec2-user@172.31.35.207:/home/ec2-user/apache-tomcat9/webapps/
                    """
                }
            }
        }
    }
}
