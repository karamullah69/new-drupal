pipeline {
    agent any

    environment {
        DRUPAL_ROOT = '/var/www/html'  // Adjust the Drupal root directory
        DRUSH = '/usr/local/bin/drush'  // Adjust the path to Drush
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check out the code from your Git repository
                    git 'https://karamullah69/new-drupal.git'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    
                    // Perform any build steps if necessary
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Deploy the code to the Drupal server
                    sh "rsync -avz --delete ./ ${DRUPAL_ROOT}/"
                    
                    // Run Drush commands to update Drupal database and clear cache
                    sh "${DRUSH} -y -r ${DRUPAL_ROOT} updatedb"
                    sh "${DRUSH} -y -r ${DRUPAL_ROOT} cache:rebuild"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
