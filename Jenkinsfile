pipeline {
    agent any

    environment {
        MAVEN_HOME = tool 'Maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/leonardoitiki/MobEAD.git'
            }
        }

        stage('Build') {
            steps {
                echo "Compilando o projeto..."
                bat "${MAVEN_HOME}\\bin\\mvn clean package"
            }
        }

        stage('Automated Tests') {
            steps {
                echo "Executando testes..."
                bat "${MAVEN_HOME}\\bin\\mvn test"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('SonarQube') {
                        bat "${scannerHome}\\bin\\sonar-scanner -Dsonar.projectKey=MobEAD -Dsonar.sources=src"
                    }
                }
            }
        }

        stage('Deploy to Development') {
            steps {
                echo "Fazendo deploy no ambiente de desenvolvimento..."
                bat 'copy target\\*.war C:\\Tomcat\\webapps\\dev-app.war'
            }
        }

        stage('Approval for Production') {
            steps {
                input message: "Deseja liberar para Produção?", ok: "Deploy"
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "Fazendo deploy no ambiente de produção..."
                bat 'copy target\\*.war C:\\Tomcat\\webapps\\prod-app.war'
            }
        }
    }

    post {
        success {
            echo "Pipeline executada com sucesso!"
        }
        failure {
            echo "Pipeline falhou!"
        }
    }
}
