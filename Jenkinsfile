pipeline {
    agent any

    stages {
        // --- Clonar el repositorio público de GitHub ---
        stage('Checkout GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/bonanza1958/miproyectoazure.git'
            }
        }

        // --- Construir y subir la imagen Docker a Docker Hub ---
        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-creds',  // Asegúrate que este ID exista en Jenkins
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PWD'
                    )]) {
                        sh """
                            echo "🔨 Construyendo imagen Docker..."
                            docker build -t guillemetal/miproyectoazure:latest .

                            echo "🔑 Logueando en Docker Hub..."
                            echo \$DOCKER_PWD | docker login -u \$DOCKER_USER --password-stdin

                            echo "🚀 Pushing de la imagen a Docker Hub..."
                            docker push guillemetal/miproyectoazure:latest
                        """
                    }
                }
            }
        }

        // --- Desplegar la aplicación en AKS ---
        stage('Deploy to AKS') {
            steps {
                sh '''
                    echo "☸️ Aplicando deployment en AKS..."
                    kubectl apply -f deployment.yaml
                '''
            }
        }

        // --- Obtener la IP pública ---
        stage('Get Public IP') {
            steps {
                sh '''
                    echo "⏳ Esperando asignación de IP pública..."
                    sleep 15
                    echo "✅ IP Pública de tu aplicación:"
                    kubectl get svc mi-app-html-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
                    echo ""
                '''
            }
        }
    }
}
