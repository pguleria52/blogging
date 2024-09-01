pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }

    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/pguleria52/blogging.git'
            }
        }
        
         stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
         stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
         stage('Trivy FS Scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }
        
         stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogginng -Dsonar.projectKey=Blogginng \
                           -Dsonar.java.binaries=target'''
                }
            }
        }
        
         stage('Build') {
            steps {
                sh "mvn package"
            }
        }
        
         stage('Publish the artifact') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                }
            }
        }

         stage('Docker Build and Tag') {
            steps {
                script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                sh "docker build -t pguleria52/blogging-app:latest ."
              }
              }
            }
        }
        
    }
}
