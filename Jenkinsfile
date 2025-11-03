pipeline {
    agent any

    environment {
        // Nome do Maven configurado em "Gerenciar Jenkins > Ferramentas Globais"
        MAVEN_HOME = tool 'Maven'
        // Nome do Sonar configurado em "Gerenciar Jenkins > Configurar Sistema"
        SONARQUBE = 'SonarQube'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Clonando repositório..."
                git branch: 'main', url: 'https://github.com/leonardoitiki/MobEAD.git'
            }
        }

        stage('Build') {
            steps {
                echo "Compilando o projeto (Maven)..."
                bat "\"%MAVEN_HOME%\\bin\\mvn\" clean package -DskipTests"
            }
        }

        stage('Automated Tests') {
            steps {
                echo "Executando testes automatizados..."
                bat "\"%MAVEN_HOME%\\bin\\mvn\" test"
            }
            post {
    success {
        script {
            if (fileExists('target/surefire-reports')) {
                junit 'target/surefire-reports/*.xml'
            } else {
                echo "Nenhum relatório JUnit encontrado — possivelmente sem testes."
            }
        }
    }
    failure {
        echo "Falha durante a execução da pipeline!"
    }
}
        }

       
        stage('Deploy to Development') {
            steps {
                echo "Fazendo deploy no ambiente de Desenvolvimento..."
                // Ajuste o caminho do Tomcat local
               bat 'copy target\\*.jar "C:\\Program Files\\Apache Software Foundation\\Tomcat9\\webapps\\dev-app.jar" /Y'
                
            }
        }

        stage('Approval for Production') {
            steps {
                input message: "Deseja liberar o deploy para Produção?", ok: "Continuar"
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "Fazendo deploy no ambiente de Produção..."
                bat 'copy target\\*.jar "C:\\Program Files\\Apache Software Foundation\\Tomcat9\\webapps\\dev-app.jar" /Y'

            }
        }
    }

    post {
        success {
            echo "Pipeline finalizada com sucesso!"
        }
        failure {
            echo "Falha durante a execução da pipeline!"
        }
    }
}
