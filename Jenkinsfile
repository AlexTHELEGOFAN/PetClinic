pipeline {
    agent any

    environment {
        // Définir les variables d'environnement si nécessaire
        GIT_URL = 'https://github.com/AlexTHELEGOFAN/PetClinic'
        PROJECT_DIR = 'PetClinic'
    }

    stages {
        stage('Prepare Classes') {
            steps {
                script {
                    // Vérifier si le dossier existe et le nettoyer
                    if (isUnix()) {
                        sh """
                        if [ -d "${PROJECT_DIR}" ]; then
                            echo "Cleaning existing directory"
                            rm -rf ${PROJECT_DIR}
                        fi
                        """
                    } else {
                        powershell """
                        if (Test-Path -Path "${PROJECT_DIR}") {
                            Write-Host "Cleaning existing directory"
                            Remove-Item "${PROJECT_DIR}" -Force -Recurse
                        }
                        """
                    }
                }
            }
        }

        stage('Clone Repository') {
            steps {
                script {
                    // Cloner le dépôt Git
                    if (isUnix()) {
                        sh "git clone ${GIT_URL} ${PROJECT_DIR}"
                    } else {
                        powershell "git clone ${GIT_URL} ${PROJECT_DIR}"
                    }
                }
            }
        }
        
        stage('Install Dependencies and Build') {
            steps {
                script {
                    dir("${PROJECT_DIR}") {
                        // Exécuter Maven Clean Install
                        if (isUnix()) {
                            sh 'mvn clean install'
                        } else {
                            powershell 'mvn clean install'
                        }
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    dir("${PROJECT_DIR}") {
                        // Exécuter Maven Test
                        if (isUnix()) {
                            sh 'mvn test'
                        } else {
                            powershell 'mvn test'
                        }
                    }
                }
            }
        }
    
    }
    
    post {
        always {
            // Publier les résultats des tests JUnit
            junit '**/target/surefire-reports/*.xml'

            echo 'Sending email notification...'
            // Configurer pour envoyer des emails ici, si nécessaire
            // mail subject: 'Test Report', body: 'See the detailed test report here.'
        }
    }
}
