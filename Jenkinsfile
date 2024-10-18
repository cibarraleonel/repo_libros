pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube' // Nombre de la conexión a tu servidor SonarQube configurado en Jenkins
        MAVEN_OPTS = '-Dmaven.test.failure.ignore=true' // Ignorar fallos en pruebas si es necesario
    }

    stages {
        stage('Checkout') {
            steps {
                // Clonar el repositorio
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                // Construir el proyecto y ejecutar pruebas con Maven
                script {
                    sh 'mvn clean verify'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Realizar el análisis de SonarQube
                script {
                    sh '''
                    mvn sonar:sonar \
                      -Dsonar.projectKey=Pipeline-AppLibros \
                      -Dsonar.projectName="Pipeline-AppLibros" \
                      -Dsonar.host.url=http://192.168.1.38:9000 \
                      -Dsonar.token=sqp_552301f6394a3d4fc17531a1dabf93a3939fe329 \
                      -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'El análisis de SonarQube fue exitoso.'
        }
        failure {
            echo 'El análisis de SonarQube falló.'
        }
    }
}
