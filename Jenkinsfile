pipeline {
    agent any

    environment {
    DOCKER_IMAGE_NAME = "java-app"
    DOCKER_IMAGE_TAG = "1.0"
    DOCKER_IMAGE = "${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
}

    tools {
    maven 'Maven'
}

    stages {
        stage('Preparar entorno') {
            steps {
                script {
                    // Cambiar al directorio del proyecto
                    dir('~/Programming/DevOps with Docker (University of Helsinki)/Challenge DevOps ELDAR/EJERCICIO 4 - JENKINS') {
                        // Verificar que el Jenkinsfile está presente
                        sh 'ls -l'
                    }
                }
            }
        }
        
        stage('Clonar repositorio') {
            steps {
                git branch: 'main', url: 'https://github.com/challengerepos/java.git'
            }
        }

        stage('Instalar dependencias') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Compilar') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Ejecutar pruebas') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Construir imagen Docker') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Subir imagen Docker') {
            steps {
                script {
                        docker.image("${DOCKER_IMAGE}").push()
                    }
                }
            }

        stage('Desplegar aplicación') {
            steps {
                script {
                    // Copiar los archivos necesarios al directorio de despliegue
                    sh 'mkdir -p deployment'
                    sh 'cp target/*.jar deployment/'
                    sh 'cp Dockerfile deployment/'
                    sh 'cp docker-compose.yml deployment/'
                    sh 'cp nginx.conf deployment/'
                    
                    // Desplegar usando Docker Compose
                    dir('deployment') {
                        sh 'docker-compose down || true'
                        sh 'docker-compose up -d --build'
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}