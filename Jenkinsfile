pipeline {
    
    agent any
    
    environment{
        SONAR_HOME= tool "sonar"
        EMAIL_TO = 'brijesh.pal@hotmail.com'
    }
    stages {
        stage('Clone code from GitHub') {
            steps {
                git url: "https://github.com/palbrijesh/neogym-fitness.git", branch: "master"
            }
        }
        stage('SonarQube Quality Analysis') {
            steps {
                withSonarQubeEnv("sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=neogym-fitness -Dsonar.projectKey=neogym-fitness"
                }
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'odc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                echo "Dependency Check --- Done"
            }
        }
        stage('Sonar Quality Gate Scan') {
            steps {
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage('Trivy File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage('Deploy') {
            steps {
                echo "Code deployment"
            }
        }
    }
    
    post {
        always {
            emailext (
                subject: "${env.PROJECT_NAME} - Build ${env.BUILD_NUMBER} - ${env.BUILD_STATUS}",
                body: 'A Test EMail',
                to: "$EMAIL_TO",
                mimeType: "text/html",
                attachmentsPattern: '**/*.log'
            )
        }
    }
}
