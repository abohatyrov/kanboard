pipeline {
    agent {
        kubernetes {
            yamlFile 'build-pod.yaml'
            defaultContainer 'docker'
        }
    }

    environment {
        DOCKER_REGISTRY = "kanboard.azurecr.io"
        DOCKER_REGISTRY_CREDENTIALS = "azure-credentials"
        DOCKER_IMAGE = "kanboard"
        app = ""
    }

    stages {
        stage('Get a PHP image') {
            steps {
                sh 'php --version'
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
                    docker.withRegistry('https://${env.DOCKER_REGISTRY}', '${env.DOCKER_REGISTRY_CREDENTIALS}') {
                        app.push(${env.BUILD_NUMBER})
                        app.push('latest')
                    }
                }
            }
        }
    }
}