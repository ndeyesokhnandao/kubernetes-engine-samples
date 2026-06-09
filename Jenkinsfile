pipeline {
    agent any

    tools {
        // Version de Snyk configurée dans l'étape précédente
        snyk 'Snyk-Latest'
    }

    environment {
        // Jenkins va injecter le Token récupéré précédemment
        SNYK_TOKEN = credentials('snyk-token')
    }

    stages {
        stage('1. Récupération du Code') {
            steps {
                // Télécharge ton fork sur le serveur Jenkins
                checkout scm
            }
        }

        stage('2. Analyse DevSecOps (Snyk)') {
            steps {
                script {
                    echo "Lancement du scan global sans compilation (No-Build)..."
                    // failOnIssues: false permet de ne pas bloquer le pipeline en cas de faille,
                    // pour que l'envoi du mail (post-actions) se déclenche toujours.
                    snykSecurity(
                        snykInstallation: 'Snyk-Latest',
                        tokenCredentialId: 'snyk-token',
                        failOnIssues: false,
                        additionalArguments: '--all-projects --severity-threshold=medium'
                    )
                }
            }
        }
    }

    post {
        always {
            echo "Génération et envoi du rapport d'analyse..."
            // Remplace par ton adresse email pour recevoir les alertes
            mail to: 'ton-adresse-email@ecole.com',
                 subject: "[DevSecOps] Rapport Snyk - Build #${env.BUILD_NUMBER}",
                 body: """Bonjour,
                 
                 Le scan de sécurité automatique requis pour le projet a été exécuté.
                 
                 Résultat global de l'exécution : ${currentBuild.currentResult}
                 Nom du projet analysé : kubernetes-engine-samples
                 Numéro de build : ${env.BUILD_NUMBER}
                 
                 Vous pouvez visualiser la liste complète des failles détectées (Code & Kubernetes) directement sur Jenkins ici : 
                 ${env.BUILD_URL}snyk/
                 
                 Cordialement,
                 Votre pipeline Jenkins CI/CD."""
        }
    }
}
