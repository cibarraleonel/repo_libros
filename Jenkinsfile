pipeline {
    agent none //any
    
    tools {
        maven 'Maven' // Reemplaza 'Maven 3.8.1' por el nombre que configuraste en la instalación de Maven
    }

    stages {
        stage('Compilar Proyecto') {
            steps {
                // Comando para compilar el proyecto usando Maven
                sh 'mvn clean install'
            }
        } 
        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonarScanner' // Nombre que diste al SonarQube Scanner en la configuración
            }
            steps {
                withSonarQubeEnv('sonarqube') { // 'SonarQube' es el nombre que configuraste en la sección SonarQube Servers

                    withCredentials([string(credentialsId: 'sonarqube', variable: 'SONARQUBE_TOKEN')]){
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Pipeline-AppLibros -Dsonar.sources=src -Dsonar.java.binaries=target/classes -Dsonar.token=$SONARQUBE_TOKEN "
                    }
                }
            }
        }
        stage('Ejecutar Pruebas') {
            steps {
                // Comando para ejecutar los tests usando Maven
                sh 'mvn test'
            }
        }

        stage('Build Docker Image'){
            
            agent any

            environment {
                // Variables de entorno a nivel global
                DOCKER_USERNAME = 'cibarraleonel'
                DOCKER_PASSWORD =  'PASSWORD_DOCKERHUB'
                DOCKER_IMAGE_NAME = 'cibarraleonel/ddsdeploy-TP'
            }

            steps{
                sh 'docker build -t ${DOCKER_IMAGE_NAME}:latest .'
                sh 'docker login -u ${DOCKER_USERNAME} - p ${DOCKER_PASSWORD}'
                sh 'docker push ${DOCKER_IMAGE_NAME}:latest'
            }
        }


        
   }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true // Archiva los artefactos generados por Maven
            junit '**/target/surefire-reports/*.xml' // Publica resultados de las pruebas (JUnit/Surefire reports)
        }
    }
}
