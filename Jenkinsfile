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
                def app
                
                app = docker.build("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}")
                docker.withRegistry('https://${env.DOCKER_REGISTRY}', '${env.DOCKER_REGISTRY_CREDENTIALS}') {
                    app.push(${env.BUILD_NUMBER})
                    app.push('latest')
                }
            }
        }
    }
}