pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Fzshaik829793/Test']]])
                sh '/root/apache-maven-3.9.4/mvn clean install''
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def gitCommitId = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    def dockerImageName = "fzshaik8297:${gitCommitId}"
                    sh "docker build -t ${dockerImageName} ."
                    env.DOCKER_IMAGE_NAME = dockerImageName
                }
            }
        }
        stage('Push Image to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'fayaz-docker-id', variable: 'dockerhubpwd')]) {
                        sh "docker login -u fzshaik8297 -p ${dockerhubpwd}"
                        sh "docker push ${env.DOCKER_IMAGE_NAME}"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def kubeconfigPath = 'path/to/your/kubeconfig/file'
                    withCredentials([file(credentialsId: 'fayaz-kube-id', variable: kubeconfigPath)]) {
                        sh "helm upgrade --install fayaz-app ./fayaz"
                        sh "--set image.repository=${env.DOCKER_IMAGE_NAME}"
                    }
                }
            }
        }
    }
}
