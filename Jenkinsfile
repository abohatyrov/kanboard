pipeline {
    agent {
        kubernetes {
            yamlFile 'build-pod.yaml'
            defaultContainer 'docker'
        }
    }

    environment {
        DOCKER_REGISTRY = "kanboard.azurecr.io"
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
                    docker.withRegistry('https://${env.DOCKER_REGISTRY}', 'azure-credentials') {
                        app.push(${env.BUILD_NUMBER})
                        app.push('latest')
                    }
                }
            }
        }
    }
}