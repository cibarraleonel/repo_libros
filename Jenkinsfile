pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Compila tu proyecto aquí, ajusta el comando según tu herramienta de construcción
                    sh 'mvn clean package' // o 'gradle build'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Añade la propiedad sonar.java.binaries
                    sh '''
                    sonar-scanner \
                        -Dsonar.host.url=http://192.168.1.38:9000/ \
                        -Dsonar.projectKey=Pipeline-AppLibros \
                        -Dsonar.projectName=Pipeline-AppLibros \
                        -Dsonar.sources=src/main/java \
                        -Dsonar.java.binaries=target/classes \
                        -Dsonar.tests=src/test/java
                    '''
                }
            }
        }
    }
}
