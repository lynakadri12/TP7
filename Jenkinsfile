pipeline{
agent any
stages{
stage('build'){
steps{
bat './gradlew build'
archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
}
}

}
}