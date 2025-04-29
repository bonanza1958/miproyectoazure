pipeline {
    agent {
        kubernetes {
            label 'docker-agent'  // El label del PodTemplate en Jenkins
            defaultContainer 'jnlp'  // El contenedor que ejecuta el agente
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins-agent: true
spec:
  containers:
  - name: jnlp
    image: jenkins/inbound-agent
    volumeMounts:
      - name: docker-socket
        mountPath: /var/run/docker.sock
  - name: docker
    image: docker:latest
    command:
      - cat
    tty: true
    volumeMounts:
      - name: docker-socket
        mountPath: /var/run/docker.sock
  volumes:
    - name: docker-socket
      hostPath:
        path: /var/run/docker.sock
            """
        }
    }

    stages {
        // --- Construye y sube la imagen a Docker Hub ---
        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'credentialsId',  // ID de las credenciales en Jenkins para Docker Hub
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PWD'
                    )]) {
                        sh '''
                            echo "DOCKER_USER: \$DOCKER_USER"
                            echo "DOCKER_PWD: \$DOCKER_PWD"
                            docker --version  # Verifica si Docker está disponible en el contenedor
                            echo \$DOCKER_PWD | docker login -u \$DOCKER_USER --password-stdin
                            docker build -t guillemetal/miproyectoazure:latest .
                            docker push guillemetal/miproyectoazure:latest
                        '''
                    }
                }
            }
        }

        // --- Despliega en AKS ---
        stage('Deploy to AKS') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }

        // --- Muestra la IP pública ---
        stage('Get Public IP') {
            steps {
                script {
                    sh '''
                        sleep 15
                        echo "✅ Aplicación desplegada. IP pública:"
                        kubectl get svc mi-app-html-service -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
                    '''
                }
            }
        }
    }
}
