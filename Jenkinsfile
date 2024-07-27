pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'JDK'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/challengerepos/java.git'
            }
        }

        stage('Install Dependencies and Compile') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Create Nginx Config') {
            steps {
                script {
                    writeFile file: 'nginx.conf', text: '''
events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    server {
        listen 80;
        server_name localhost;

        location / {
            root   /usr/share/nginx/html/myapp;
            index  index.html index.htm;
        }

        location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|html|htm)$ {
            root /usr/share/nginx/html/myapp;
        }

        location ~ \\.(jsp|do)$ {
            proxy_pass http://localhost:8082;
        }
    }
}
'''
                }
            }
        }

        stage('Create Dockerfile') {
            steps {
                script {
                    writeFile file: 'Dockerfile', text: '''
FROM nginx:alpine

COPY target/*.war /tmp/app.war

RUN apk add --no-cache openjdk8-jre unzip

RUN mkdir -p /usr/share/nginx/html/myapp && \
    unzip /tmp/app.war -d /usr/share/nginx/html/myapp && \
    rm /tmp/app.war

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
'''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("java-app:${env.BUILD_ID}")
                }
            }
        }

        stage('Deploy to Nginx') {
            steps {
                script {
                    sh '''
                    docker stop java-nginx-app || true
                    docker rm java-nginx-app || true
                    docker run -d --name java-nginx-app -p 8082:80 java-app:${BUILD_ID}
                    '''
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