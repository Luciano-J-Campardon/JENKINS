pipeline {
    agent any

    environment {
        // Define variables de entorno necesarias aquí, si las hay
        PORT = '80'
    }

    stages {
        stage('Clonar Repositorio') {
            steps {
                git 'https://github.com/challengerepos/java'
            }
        }

        stage('Instalar Dependencias') {
            steps {
                script {
                    // Suponiendo que usas Maven para gestionar las dependencias
                    sh 'mvn clean install'
                }
            }
        }

        stage('Compilar Código') {
            steps {
                script {
                    // Empaquetar el proyecto en un JAR
                    sh 'mvn package'
                }
            }
        }

        stage('Preparar Servidor Web') {
            steps {
                script {
                    // Instalación de Node.js y http-server si no están instalados
                    sh 'npm install -g http-server'

                    // Crear una carpeta para el despliegue y copiar el JAR allí
                    sh 'mkdir -p /tmp/app'
                    sh 'cp target/*.jar /tmp/app/app.jar'

                    // Crear un archivo index.html para servir el JAR (opcional)
                    writeFile file: '/tmp/app/index.html', text: '<html><body><h1>Application Deployed</h1></body></html>'
                }
            }
        }

        stage('Desplegar en Servidor Web') {
            steps {
                script {
                    // Iniciar un servidor web simple usando http-server
                    sh 'http-server /tmp/app -p $PORT'
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Limpiar el workspace después de cada ejecución
        }
    }
}