pipeline {
    agent any
    stages {
        // --- Clona el código de GitHub ---
        stage('Checkout GitHub') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/bonanza1958/miproyectoazure.git'  // URL HTTPS actualizada
                // Alternativa con SSH: url: 'git@github.com:bonanza1958/miproyectoazure.git'
            }
        }

        // --- Construye y sube la imagen a Docker Hub ---
        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-creds',  // Credenciales de Docker Hub en Jenkins
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
                sh 'kubectl apply -f deployment.yaml'  // Asegúrate de que el YAML tenga el nombre correcto del servicio
            }
        }

        // --- Muestra la IP pública ---
        stage('Get Public IP') {
            steps {
                sh '''
                    sleep 15  // Espera un poco más para que Azure asigne la IP
                    echo "✅ Aplicación desplegada. IP pública:"
                    kubectl get svc mi-app-html-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
                '''
            }
        }
    }
}