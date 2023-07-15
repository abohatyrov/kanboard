pipeline {
    agent "Kanboard Build"
    environment {
        VERSION = "fe91f0611"
    }
    stages {
        stage ("Install Dependencies") {
            sh "composer install --ignore-platform-reqs --no-scripts --no-dev --no-interaction --no-progress"
            sh "composer update --ignore-platform-reqs --no-scripts --no-dev --no-interaction --no-progress"
            sh "composer dump-autoload --optimize --no-interaction"
        }

        stage ("Run Tests") {
            sh "vendor/bin/phpunit -c tests/units.mysql.xml --verbose --coverage-text --colors=never"
        }

        stage ("Make arcihve") {
            sh "git archive --format=zip --prefix=kanboard/ $(VERSION) -o kanboard-$(VERSION).zip"
        }
    }
}