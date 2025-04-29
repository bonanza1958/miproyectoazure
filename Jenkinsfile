pipeline {
    agent {
        kubernetes {
            label 'docker-agent'
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins-agent: true
spec:
  securityContext:
    runAsUser: 0  # Ejecutar como root para acceder al Docker socket
  containers:
  - name: jnlp
    image: guillemetal/jenkins-agent-docker:latest  # Imagen personalizada que incluye Docker
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
        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'credentialsId',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PWD'
                    )]) {
                        sh '''
                            echo "DOCKER_USER: \$DOCKER_USER"
                            echo "DOCKER_PWD: \$DOCKER_PWD"
                            docker --version
                            echo \$DOCKER_PWD | docker login -u \$DOCKER_USER --password-stdin
                            docker build -t guillemetal/miproyectoazure:latest .
                            docker push guillemetal/miproyectoazure:latest
                        '''
                    }
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }

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
