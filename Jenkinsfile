pipeline {
    agent any

    tools {
        jdk 'jdk1'
        maven 'maven1'
    }

    environment {
        SCANNER_HOME = tool 'sonarqube'
        DOCKER_IMAGE = "hajarelmaimouni/santa"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git changelog: false, poll: false,
                    url: 'https://github.com/jaiswaladi246/secretsanta-generator.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp-dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh """
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=myproject \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target/classes \
                        -Dsonar.host.url=http://10.21.85.13:9000 \
                        -Dsonar.token=sqp_1b408297a083842ed6609c983c7b1b9c4711c68e
                    """
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-hub-credentials', toolName: 'docker') {
                        sh "docker build -t santa ."
                        sh "docker tag santa ${DOCKER_IMAGE}:latest"
                        sh "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                script {
                    sh """
                        trivy image \
                        --timeout 10m \
                        --exit-code 0 \
                        --severity HIGH,CRITICAL \
                        --scanners vuln \
                        ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh 'docker stop santa-app || true'
                    sh 'docker rm santa-app || true'
                    sh "docker run -d -p 8081:8080 --name santa-app ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('OWASP ZAP - DAST') {
            steps {
                script {
                    sh """
                        chmod 777 \$(pwd)
                        docker run --rm \
                        --user root \
                        -v \$(pwd):/zap/wrk/:rw \
                        ghcr.io/zaproxy/zaproxy:stable \
                        zap-baseline.py \
                        -t http://10.21.85.13:8081 \
                        -r zap-report.html \
                        -I
                    """
                }
            }
        }

    }

    post {
        always {
            echo "Pipeline terminé. Nettoyage..."
            sh 'docker stop santa-app || true'
            sh 'docker rm santa-app || true'
            archiveArtifacts artifacts: 'zap-report.html', allowEmptyArchive: true
        }
        success {
            echo "Pipeline CI/CD terminé avec succès !"
        }
        failure {
            echo "Pipeline échoué. Vérifiez les logs."
        }
    }
}
