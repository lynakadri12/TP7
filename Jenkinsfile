pipeline{
agent any
stages{
stage('build'){
steps{
               script {
                    bat 'dir'
                    bat '.\\gradlew.bat build'
                }
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
}
}

}
}