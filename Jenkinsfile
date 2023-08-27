pipeline {
    agent {
        kubernetes {
            yamlFile 'k8s/build-pod.yaml'
            defaultContainer 'docker'
        }
    }

    environment {
        DOCKER_REGISTRY = "kanboard.azurecr.io"
        DOCKER_REGISTRY_URL = "https://kanboard.azurecr.io"
        DOCKER_REGISTRY_CREDS = "azure-credentials"
        DOCKER_IMAGE = "kanboard"
        ARGOCD_SERVER="https://20.62.153.129/"
        APP_NAME="kanboard"
        app = ""
    }

    stages {
        stage('Get a Docker version') {
            steps {
                sh 'docker --version'
            }
        }
        

        stage('Run tests') {
            steps {
                container('tests') {
                    sh 'echo "Skip tests..."'
                }
            }
        }

        stage('Build and push image') {
            steps {
                script {
                    def app = docker.build("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}")
                    docker.withRegistry("${env.DOCKER_REGISTRY_URL}", "${env.DOCKER_REGISTRY_CREDS}") {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }  
    }
}