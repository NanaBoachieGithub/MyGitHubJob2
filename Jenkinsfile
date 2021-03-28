node {

def mvnHome = tool 'Maven3'
stage ('Checkout') {

checkout scm
}
stage ('Build') {
sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml"
}
stage ('Code Quality Scan') {
withSonarQubeEnv('SonarQube') {
sh "${mvnHome}/bin/mvn sonar:sonar -f MyWebApp/pom.xml"
   }
}
    stage ('Nexus Upload')
    {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: '18.224.4.206:8081',
        groupId: 'com.mkyong',
        version: '1.0-SNAPSHOT',
        repository: 'maven-snapshots',
        credentialsId: 'f48040f9-6da0-4dbb-8f45-0b0a556caa82',
        artifacts: [
            [artifactId: 'MyWebApp',
             classifier: '',
             file: 'MyWebApp/target/MyWebApp.war',
             type: 'war']
        ]
     )
    }

   stage ('DEV Deploy')  {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat8(credentialsId: 'e7d5082b-1cb3-4103-8604-5f63142472e3', path: '', url: 'http://localhost:8090')], contextPath: null, war: '**/*.war'
    }
stage ('DEV Approve') {
echo "Taking approval from DEV"
timeout(time: 7, unit: 'DAYS') {
input message: 'Do you want to deploy?', submitter: 'admin'
     }
 }


stage ('Slack Notification') {


    slackSend(channel:'#jenkinscolab', message: "MO, Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")


  }

}
