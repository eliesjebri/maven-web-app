pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/eliesjebri/maven-web-app.git'
            }
        }
        stage('Build Stage') {
            steps {
                sh "mvn clean package"
            }

            tools {
                maven 'Maven-3.8.6'
            }
        }
        stage('Code Review') {
            steps {
                withSonarQubeEnv('SonarQube-community-10.3.0') {
                    sh "mvn clean package sonar:sonar"
   	                echo 'Static Analysis Completed' 
                }
            }

            tools {
                maven 'Maven-3.8.6'
            }
        }
        stage('Upload Build Artifact') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: '01-maven-web-app', classifier: '', file: 'target/01-maven-web-app.war', type: 'war']], credentialsId: 'Nexus-Creds', groupId: 'in.elies', nexusUrl: '192.168.0.155:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-web-app-snapshots', version: '1.0-SNAPSHOT'
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                script {
            		def remote = [:]
            		remote.name = '192.168.0.154'
            		remote.host = '192.168.0.154'
            		remote.user = 'root'
            		remote.password = 'password'
            		remote.allowAnyHosts = true      
            		writeFile file: 'target/01-maven-web-app.war', text: 'ls -lrt'
            		sshPut remote: remote, from: 'target/01-maven-web-app.war', into: '/var/lib/tomcat/webapps'
                }
            }
        }
    }
}