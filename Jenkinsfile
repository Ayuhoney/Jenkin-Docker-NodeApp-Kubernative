pipeline {
    agent any
    environment {
        KUBECONFIG = credentials('kuber')
    }
    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Test") {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage("Build") {
            steps {
                sh 'npm run build'
            }
        }

        stage("Build Image") {
            steps {
                sh 'docker build -t my-node-app:1.0 .'
            }
        }
        
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                    sh 'docker tag my-node-app:1.0 ayushrudra/my-node-app:1.0'
                    sh 'docker push ayushrudra/my-node-app:1.0'
                    sh 'docker logout'
                }
            }
        }
        
        stage("Deploy to Kubernetes") {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kuber', variable: 'KUBECONFIG')]) {
                        sh 'kubectl apply -f kubernative-compose.yaml'
                    }
                }
            }
        }
    }
}
