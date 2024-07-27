pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'java-app'
        DOCKER_IMAGE_TAG = '1.0'
        DOCKER_IMAGE = "${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
    }

    tools {
        maven 'Maven'
    }

    stages {
        stage('Clonar repositorio') {
            steps {
                git branch: 'main', url: 'https://github.com/challengerepos/java.git'
            }
        }

        stage('Instalar dependencias') {
            steps {
                sh 'mvn clean install'
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

        stage('Desplegar en Nginx') {
            steps {
                script {
                    // Copiar el archivo WAR generado a una ubicación temporal
                    sh 'cp target/mi-aplicacion.war /tmp/mi-aplicacion.war'
                    
                    // Crear un Dockerfile para construir la imagen con Nginx
                    writeFile file: 'Dockerfile', text: '''
                    FROM nginx:alpine
                    COPY /tmp/mi-aplicacion.war /usr/share/nginx/html/mi-aplicacion.war
                    EXPOSE 80
                    '''

                    // Construir la imagen Docker
                    sh 'docker build -t mi-aplicacion-nginx .'

                    // Ejecutar el contenedor de Docker
                    sh 'docker run -d -p 80:80 mi-aplicacion-nginx'
                }
            }
        }
    }
}

