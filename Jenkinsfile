pipeline {
    agent any
    stages {
        // --- Construye y sube la imagen a Docker Hub ---
        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'credentialsId',  // ID de las credenciales en Jenkins
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PWD'
                    )]) {
                        sh """
                            docker build -t guillemetal/miproyectoazure:latest .
                            echo \$DOCKER_PWD | docker login -u \$DOCKER_USER --password-stdin
                            docker push guillemetal/miproyectoazure:latest
                        """
                    }
                }
            }
        }

        // --- Despliega en AKS ---
        stage('Deploy to AKS') {
            steps {
                sh 'kubectl apply -f deployment.yaml'  // Apunta al deployment.yaml correcto
            }
        }

        // --- Muestra la IP pública ---
        stage('Get Public IP') {
            steps {
                sh '''
                    sleep 15  // Espera para que se asigne la IP pública
                    echo "✅ Aplicación desplegada. IP pública:"
                    kubectl get svc mi-app-html-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
                '''
            }
        }
    }
}
