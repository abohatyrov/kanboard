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

        stage('Deploy to Kubernetes') {
            steps {
                step([$class: 'KubernetesEngineBuilder', \
                  manifestPattern: 'k8s/deployment.yaml', \
                  verifyDeployments: true])
            }
        }
    }
}