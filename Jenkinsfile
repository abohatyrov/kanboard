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
        
        // stage('Install dependencies') {
        //     steps {
                
        //         sh 'composer install'
        //     }
        // }

        // stage('Run tests') {
        //     steps {
        //         sh 'make test-mysql'
        //     }
        // }

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

        stage ('Deploy_K8S') {
            steps {
                container('argocd') {
                    withCredentials([string(credentialsId: "argocd-deploy-role", variable: 'ARGOCD_AUTH_TOKEN')]) {
                        sh 'argocd app sync ${env.APP_NAME} --auth-token ${env.ARGOCD_AUTH_TOKEN} --grpc-web ${env.ARGOCD_SERVER}'
                        sh 'argocd app wait ${env.APP_NAME} --auth-token ${env.ARGOCD_AUTH_TOKEN} --grpc-web ${env.ARGOCD_SERVER}'
                    }
                }
            }
        }
    }
}