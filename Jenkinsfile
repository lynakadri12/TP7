pipeline{
    agent any
    stages{
        stage('build'){
            steps{
                bat 'gradle build'
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }
    }
}