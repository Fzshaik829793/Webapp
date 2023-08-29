pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Fzshaik829793/Test']]])
                sh '/var/lib/jenkins/maven/bin/mvn clean install'

            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImageName = "fzshaik8297/devops-integration:v9.3.1"
                    sh "sudo docker build -t ${dockerImageName} ."
                    env.DOCKER_IMAGE_NAME = dockerImageName
                }
            }
        }
        stage('Push Image to Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'fayaz-dockerhub-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u fzshaik8297 -p Fayaz@9052"
                        sh "docker push ${env.DOCKER_IMAGE_NAME}"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def kubeconfigPath = '/var/lib/jenkins/.kube/config'
                    withCredentials([file(credentialsId: 'fayaz-kube-id', variable: 'kubeconfigPath')]) {
                        env.PATH = "/usr/local/bin:${env.PATH}"
                        env.KUBECONFIG = kubeconfigPath
                        sh "helm upgrade --install fayaz-app ~/fayaz --set image.repository=fzshaik8297/devops-integration"
                    }
                }
            }
        }
    }
}
