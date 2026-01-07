pipeline{
    agent any
    stages{
        stage('build'){
            steps{
                bat 'gradlew.bat build -x test'
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }
    }
}