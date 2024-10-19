pipeline {
    agent any
    
    tools {
        maven 'Maven' // Reemplaza 'Maven 3.8.1' por el nombre que configuraste en la instalación de Maven
    }

    stages {
   //     stage('Clonar Repositorio') {
   //         steps {
                //git branch: 'main', url: 'https://hub.com/cibarraleonel/repo_libros'
   //         }
   //     }
        
        stage('Compilar Proyecto') {
            steps {
                // Comando para compilar el proyecto usando Maven
                sh 'mvn clean install'
            }
        }

        stage('Ejecutar Pruebas') {
            steps {
                // Comando para ejecutar los tests usando Maven
                sh 'mvn test'
            }
        }    
        //        steps {
                    // Comando para ejecutar los tests usando Maven
                    //sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=AppLibros -Dsonar.projectName=AppLibros -Dsonar.host.url=http://192.168.1.38:9000 -Dsonar.token=sqp_316ff4fb27b76955551aadd6b22ece6cc7193b14'
        //        }
          stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonarScanner' // Nombre que diste al SonarQube Scanner en la configuración
            }
            steps {
                withSonarQubeEnv('sonarqube') { // 'SonarQube' es el nombre que configuraste en la sección SonarQube Servers
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=mi-proyecto -Dsonar.sources=src -Dsonar.java.binaries=target/classes"
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
