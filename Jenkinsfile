pipeline {
    agent any

    tools {
        nodejs 'NodeJS-18' // Nom défini dans la Global Tool Configuration de Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/VOTRE_USER/mon-app-pipeline.git' // REMPLACEZ PAR L'URL DE VOTRE DÉPÔT GIT
            }
        }

        stage('Analyse SCA (Dépendances)') {
            steps {
                sh 'npm audit --audit-level=high'
            }
        }

        stage('Analyse SAST (SonarQube)') {
            environment {
                scannerHome = tool 'SonarScanner' // Nom défini dans la Global Tool Configuration
            }
            steps {
                withSonarQubeEnv('SonarQube') { // Nom du serveur SonarQube défini dans Jenkins
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Vérification Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build & Scan Image Docker') {
            steps {
                script {
                    def dockerImage = docker.build("mon-app-pipeline:${env.BUILD_ID}")
                    sh "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image mon-app-pipeline:${env.BUILD_ID}"
                }
            }
        }
    }
}
