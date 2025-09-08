pipeline {  // Déclare un pipeline Jenkins déclaratif
    agent any  // Le pipeline peut tourner sur n’importe quel agent (ici ta machine Windows avec Jenkins)
    parameters {
            // Menu déroulant pour choisir l'environnement (affichage seulement)
            choice(
                name: 'ENV',
                choices: ['test', 'preprod', 'prod'],
                description: 'Choisissez un environnement (affichage uniquement, non utilisé)'
            )
        }
    stages {  // Définition des étapes du pipeline

        stage('Checkout') {  // Étape 1 : Récupérer le code source depuis GitHub
            steps {
                // Clone le dépôt Git (branche main) dans l’espace de travail Jenkins
                git branch: 'main', url: 'https://github.com/Akecil25/ProjetJenkins.git'
            }
        }

        stage('Install Newman & HTMLExtra Reporter') {  // Étape 2 : Installation des dépendances nécessaires
            steps {
                bat '''
                echo Installing Newman and HTMLExtra reporter...
                // Installe Newman localement dans le projet
                call npm install newman --save-dev
                // Installe le reporter HTMLExtra localement
                call npm install newman-reporter-htmlextra --save-dev
                '''
            }
        }

        stage('Run Postman Collection') {  // Étape 3 : Exécuter la collection Postman avec Newman
            steps {
                bat '''
                echo Creating reports folder...
                // Crée le dossier "reports" si inexistant
                if not exist reports mkdir reports
                
                echo [INFO] Environnement choisi dans Jenkins: %ENV%  
                
                echo Running Postman collection with Newman...
                // Exécute la collection Postman avec plusieurs reporters
                // ^ = continuation de ligne dans Windows batch
                 npx newman run myclients.postman_collection.json ^
                  --reporters cli,htmlextra,junit ^
                  --reporter-htmlextra-export reports\\newman-report.html ^
                  --reporter-junit-export reports\\newman-report.xml
                '''
            }
        }
    }
post {
    always {
      // afficher le rapport directement dans Jenkins (onglet "Newman HTML Report")
      archiveArtifacts artifacts: 'reports/*', fingerprint: true

          publishHTML(target: [
      reportName: 'Newman HTML Report',
      reportDir: 'reports',
      reportFiles: 'newman-report.html',
      keepAll: true,
      alwaysLinkToLastBuild: true,
      allowMissing: false
    ])

    }
  }
    
}
