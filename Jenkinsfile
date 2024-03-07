pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }    
    stages {
        stage('git checkout') {
            steps {
                git branch: 'update01', url: 'https://github.com/kennygee007/Google-Kubernetes-boilerplate.git'
            }
        }

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=bouq -Dsonar.ProjectName=bouq -Dsonar.java.binaries=."
                }
            }
        }

        stage('Build and Push') {
            steps {
                script {
                    def buildAndPush = { serviceName, servicePath ->
                        withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                            sh "docker build -t kennygee009/${serviceName}:latest app/${servicePath}"
                            sh "docker push kennygee009/${serviceName}:latest"
                            sh "docker rmi kennygee009/${serviceName}:latest"
                        }
                    }
                    
                    buildAndPush('adservice', 'adservice')
                    buildAndPush('cartservice', 'cartservice/src')
                    buildAndPush('checkoutservice', 'checkoutservice')
                    buildAndPush('currencyservice', 'currencyservice')
                    buildAndPush('emailservice', 'emailservice')
                    buildAndPush('frontend', 'frontend')
                    buildAndPush('loadgenerator', 'loadgenerator')
                    buildAndPush('paymentservice', 'paymentservice')
                    buildAndPush('productcatalogservice', 'productcatalogservice')
                    buildAndPush('recommendationservice', 'recommendationservice')
                    buildAndPush('shippingservice', 'shippingservice')
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Assuming 'deployment.yml' is in the same directory as the Jenkinsfile
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: 'your-namespace', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh 'kubectl apply -f deployment.yml'
                }
            }
        }
    }
}
