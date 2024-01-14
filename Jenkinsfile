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
                    git credentialsId: '7e710353-4472-4a04-a661-0a06ac7ddcea', url: 'https://github.com/karamullah69/new-drupal.git'
                }
            }
        }

        stage('Build and Push Docker Images') {
            steps {
                script {
                    docker.build("hello-world-1:${env.BUILD_NUMBER}")
                    docker.build("hello-world-2:${env.BUILD_NUMBER}")
                    docker.withRegistry('https://your-docker-registry/', 'docker-registry-credentials') {
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
