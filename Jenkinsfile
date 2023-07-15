pipeline {
    agent { label "Kanboard Build" }
    environment {
        VERSION = "fe91f0611"
    }
    stages {
        stage ("Install Dependencies") {
            steps {
                sh 'php -v'
                sh "composer install --ignore-platform-reqs --no-scripts --no-dev --no-interaction --no-progress"
                sh "composer update --ignore-platform-reqs --no-scripts --no-dev --no-interaction --no-progress"
                sh "composer dump-autoload --optimize --no-interaction"
            }
        }

        stage ("Run Tests") {
            steps {
                echo 'Running PHPUnit tests...'
                sh 'php ./vendor/bin/phpunit -c tests/units.mysql.xml --coverage-html ./report/clover --coverage-clover ./report/clover.xml --log-junit ./report/junit.xml'
                sh 'chmod -R a+w $PWD && chmod -R a+w .'
                junit 'report/*.xml'}
        }

        stage ("Make arcihve") {
            steps {
                sh "git archive --format=zip --prefix=kanboard/ ${VERSION} -o kanboard-${VERSION}.zip"
            }
        }
    }
}