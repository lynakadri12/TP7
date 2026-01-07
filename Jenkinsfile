pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                bat 'gradlew.bat build'
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                // 1. Lancement des tests unitaires
                bat 'gradlew.bat test'

                // 2. Archivage des résultats des tests unitaires
                junit '**/build/test-results/test/*.xml'

                // 3. Génération des rapports de tests Cucumber
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'build/reports/tests/test',
                    reportFiles: 'index.html',
                    reportName: 'Test Report'
                ])
            }
        }
    }
}