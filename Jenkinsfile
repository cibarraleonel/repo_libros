pipeline {
    agent any
    
    tools {
        maven 'Maven' // Reemplaza 'Maven 3.8.1' por el nombre que configuraste en la instalación de Maven
    }

    stages {
        stage('Checkout') {
            steps {
                // Aquí clona el repositorio del proyecto
                git branch: 'main', url: 'https://github.com/cibarraleonel/repo_libros'
            }
        }


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


            environment {
                // Variables de entornO
                DOCKERHUB_CREDENTIALS =  credentials('dockerhub-token')
                DOCKERHUB_REPO = 'cibarraleonel/ddsdeploy-TP'
            }

            steps{
                script {
                    // Construye la imagen Docker con un tag "latest"
                    //docker.build("${env.DOCKERHUB_REPO}:latest")
                    docker build -t ${DOCKERHUB_REPO}:lastest .
                }
            }
        }

        stage('Push a DockerHub') {
            steps {
                script {
                    // Inicia sesión en DockerHub
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-token') {
                        // Hace el push de la imagen construida a DockerHub
                        docker.image("${env.DOCKERHUB_REPO}:latest").push()
                    }
                }
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
