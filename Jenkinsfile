pipeline {
    environment {
        DOCKER_REGISTRY = "kanboard.azurecr.io"
        DOCKER_REGISTRY_CREDENTIALS = "azure-credentials"
        DOCKER_IMAGE = "kanboard"
    }
    podTemplate(containers: [
    containerTemplate(
        name: 'php', 
        image: 'php:latest', 
        command: 'sleep', 
        args: '30d'
        )
    ]) {

        node(POD_LABEL) {
            def app

            stage('Get a PHP image') {
                container('php') {
                    sh 'php --version'
                }
            }
            
            stage('Install dependencies') {
                container('php') {
                    sh 'composer install'
                }
            }

            // stage('Run tests') {
            //     container('php') {
            //         sh 'make test-mysql'
            //     }
            // }

            stage('Build and push image') {
                container('php') {
                    app = docker.build("${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}")
                    docker.withRegistry('https://${env.DOCKER_REGISTRY}', '${env.DOCKER_REGISTRY_CREDENTIALS}') {
                        app.push(${env.BUILD_NUMBER})
                        app.push('latest')
                    }
                }
            }


        }
    }
}