pipeline {
    agent {
        kubernetes {
            label 'kanboard'
            defaultContainer 'jnlp'
            yaml """
            spec:
                containers:
                - name: php
                  image: php:latest
                  command:
                  - sleep
                  args:
                  - 30d
            """
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
        
        stage('Install dependencies') {
            steps {
                sh 'composer install'
            }
        }

        // stage('Run tests') {
        //     steps {
        //         sh 'make test-mysql'
        //     }
        // }

        stage('Build and push image') {
            steps {
                docker.build("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}")
                docker.withRegistry('https://${env.DOCKER_REGISTRY}', '${env.DOCKER_REGISTRY_CREDENTIALS}') {
                    docker.image("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}").push("${env.BUILD_NUMBER}")
                    image.push(${env.BUILD_NUMBER})
                    docker.image("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}").push("latest")
                }
            }
        }
    }
}