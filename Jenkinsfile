pipeline {
    agent none

    stages {
        stage('Checkout') {
            agent any
            steps {
                git branch: 'main',
                url: 'https://github.com/TaemHam/source-maven-java-spring-hello-webapp.git'
            }
        }

        stage('Build') {
            agent {
                docker { 
                    image 'maven:3-openjdk-8' 
                }
            }
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Image Build') {
            agent any
            steps {
                sh 'docker image build -t tomcat:hello .'
            }
        }

        stage('Image Tag') {
            agent any
            steps {
                sh 'docker image tag tomcat:hello taemham/tomcat:v1'
                sh 'docker image tag tomcat:hello taemham/tomcat:latest'
            }
        }

        stage('Image Push') {
            agent any
            steps {
                withDockerRegistry(credentialsId: 'docker-hub-token', url: 'https://index.docker/io/v1') {
                    sh 'docker image push taemham/tomcat:v1'
                    sh 'docker image push taemham/tomcat:latest'
                }
            }
        }

        stage('Running Container') {
            agent {
                docker {
                    image 'docker:dind' 
                }
            }
            steps {
                sh 'docker -H tcp://172.31.39.212:2375 run -d --name webserver -p 80:8080 taemham/tomcat:latest'
            }
        }
    }
}
