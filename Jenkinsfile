pipeline{
agent any
stages{
stage('build'){
steps{
bat 'gradlew.bat build'
archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
}
}

}
}