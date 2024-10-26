pipeline {
    agent any
    
    tools {
        maven 'Maven' // Reemplaza 'Maven 3.8.1' por el nombre que configuraste en la instalación de Maven
    }

    stages {
        stage('Compilar Proyecto') {
            agent any
            steps {
                // Comando para compilar el proyecto usando Maven
                sh 'mvn clean install'
            }
        } 
        stage('SonarQube Analysis') {
            agent any
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
            agent any

            
            steps {
                // Comando para ejecutar los tests usando Maven
                sh 'mvn test'
            }
        }

        stage('Build Docker and Push Image'){
            agent any

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
        stage('Restart Appx Pod in Minikube') {
            agent {
                label 'minikube'
            }
            options {
                skipDefaultCheckout(true)  // Evita que haga el checkout en VM2
            }
            steps {
                
                sh 'docker pull cibarraleonel/repo_libros:latest'

                // Reinicia el despliegue para cargar la nueva imagen
                sh 'minikube kubectl rollout restart deployment appx'
            }
            post {
                success{
                    sh 'echo pull de image y restart del deploymen OK!'
                }
                failure{
                    sh 'echo Falló restart de DEPLOYMENT'
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
