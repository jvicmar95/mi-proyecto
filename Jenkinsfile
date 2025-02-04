pipeline {
    agent any
    
    environment {
        // Reemplaza 'jvicmar95' con tu nombre de usuario en Docker Hub
        DOCKER_IMAGE = 'jvicmar95/mi-proyecto'  // Nombre del repositorio en Docker Hub
        DOCKER_TAG = "${env.BUILD_ID}"  // Etiqueta para la imagen (ID del build)
        
        // Asegúrate de que estos IDs coincidan con los que creaste en Jenkins
        KUBE_CONFIG = credentials('kubeconfig')  // Referencia a las credenciales del kubeconfig (ID 'kubeconfig')
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Construcción de la imagen Docker
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    // Subir la imagen a Docker Hub con las credenciales configuradas
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-creds') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                // Desplegar la imagen en Kubernetes utilizando el archivo kubeconfig
                sh """
                    kubectl --kubeconfig=$KUBE_CONFIG set image deployment/mi-deployment nginx=${DOCKER_IMAGE}:${DOCKER_TAG} --record
                """
            }
        }
    }
    
    post {
        always {
            // Limpiar la imagen Docker del agente local después del despliegue
            sh 'docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG} || true'
        }
    }
}
