pipeline {
    agent any
    
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


            environment {
                // Variables de entornO
                DOCKERHUB_PASSWORD =  credentials('dockerhub-password')
            }

            steps{
                sh 'docker build -t cibarraleonel/repo_libros .'
                sh 'docker login -u cibarraleonel -p $DOCKERHUB_PASSWORD'
                sh 'docker push cibarraleonel/repo_libros'
                sh 'docker rmi cibarraleonel/repo_libros'
            }

            post {
                success{
                    sh 'echo Build, Login y Push OK!'
                }
                failure{
                    sh 'echo Falló Build, Login y Push'
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
