pipeline {
    agent {
        node {
            label 'docker-my'
        }
    }

    environment {
        DRUPAL_ROOT = '/var/www/html'  // Adjust the Drupal root directory
        DRUSH = '/usr/local/bin/drush'  // Adjust the path to Drush
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Check out the code from your Git repository
                    scm
                }
            }
        }

        stage('Build and Push Docker Images') {
            steps {
                script {
                    docker.build("hello-world-1:${env.BUILD_NUMBER}")
                    docker.build("hello-world-2:${env.BUILD_NUMBER}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-registry-credentials') {
                        docker.image("hello-world-1:${env.BUILD_NUMBER}").push()
                        docker.image("hello-world-2:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy Drupal applications
                    kubernetesDeploy(
                        kubeconfigId: 'your-kubeconfig',
                        configs: 'drupal-app1.yaml,drupal-app2.yaml',
                        enableConfigSubstitution: true,
                        secretName: 'your-k8s-secret'
                    )

                    // Copy HTML file to Drupal pod
                    kubernetesExec(
                        kubeconfigId: 'your-kubeconfig',
                        podName: 'drupal-app1-6695984fbc-wwv8h', // Update with the actual pod name
                        containerName: 'drupal',
                        command: ['cp', '/var/www/html/hello-world.html', '/var/www/html/sites/default/']
                    )

                    kubernetesExec(
                        kubeconfigId: 'your-kubeconfig',
                        podName: 'drupal-app2-7ff667b74-kh5nz', // Update with the actual pod name
                        containerName: 'drupal',
                        command: ['cp', '/var/www/html/hello-world.html', '/var/www/html/sites/default/']
                    )
                }
            }
        }
    }
}
