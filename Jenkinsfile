pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube'
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
                sh "${MAVEN_HOME}/bin/mvn clean package"
            }
        }

        stage('Testes Automatizados') {
            steps {
                echo "Executando testes..."
                sh "${MAVEN_HOME}/bin/mvn test"
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Análise SonarQube') {
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=MobEAD -Dsonar.sources=src"
                    }
                }
            }
        }

        stage('Deploy em Desenvolvimento') {
            steps {
                echo "Realizando deploy no ambiente de Desenvolvimento..."
                sh "cp target/*.war /var/lib/tomcat9/webapps/dev-app.war"
            }
        }

        stage('Aprovação para Produção') {
            steps {
                input message: "Deseja liberar para Produção?", ok: "Deploy"
            }
        }

        stage('Deploy em Produção') {
            steps {
                echo "Realizando deploy no ambiente de Produção..."
                sh "cp target/*.war /var/lib/tomcat9/webapps/prod-app.war"
            }
        }
    }

    post {
        success {
            echo "Pipeline finalizada com sucesso!"
        }
        failure {
            echo "Falha na pipeline!"
        }
    }
}
