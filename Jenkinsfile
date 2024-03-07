pipeline {
    agent any
    stages {
        stage("checkout") {
            steps {
                checkout scm
            }
        }

        stage("Test") {
            steps {
                sh 'sudo npm install'
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
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    sh 'docker tag my-node-app:1.0 ayushrudra/my-node-app:1.0'
                    sh 'docker push ayushrudra/my-node-app:1.0'
                    sh 'docker logout'
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def kubeConfig = readFile "${env.HOME}/.kube/config" 
                    
                    sh """
                        export KUBECONFIG="${env.HOME}/.kube/config"
                        kubectl set image deployment/my-node-app my-node-app=ayushrudra/my-node-app:1.0 --record
                    """
                }
            }
        }
    }
}
