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
                echo "Compiling the project..."
                sh "${MAVEN_HOME}/bin/mvn -B clean package"
            }
        }

        stage('Automated Tests') {
            steps {
                echo "Running unit tests..."
                sh "${MAVEN_HOME}/bin/mvn test"
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=MobEAD -Dsonar.sources=src"
                    }
                }
            }
        }

        stage('Deploy to Development') {
            steps {
                echo "Deploying to Development environment..."
                sh "cp target/*.war /usr/local/tomcat/webapps/dev-app.war"
            }
        }

        stage('Approval for Production') {
            steps {
                input message: "Release to Production?", ok: "Deploy"
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "Deploying to Production environment..."
                sh "cp target/*.war /usr/local/tomcat/webapps/prod-app.war"
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
