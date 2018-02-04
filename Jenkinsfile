node {
    
 notify('Started')    
 
 stage ('SCM_Checkout') {
    checkout([$class: 'GitSCM', 
        branches: [[name: '*/master']], 
        doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], 
        userRemoteConfigs: [[url: 'https://github.com/shriniwm/Maven-petclinic-project.git']]])
 }
 stage ('Build_Test and Package') {
    sh 'mvn clean verify package'
    junit 'target/surefire-reports/TEST*.xml'
 }
 
 stage ('Archive and Notify') {
    publishHTML(target: [allowMissing: true, 
                 alwaysLinkToLastBuild: false, 
                 keepAll: true, 
                 reportDir: 'target/site/jacoco', 
                 reportFiles: 'index.html', 
                 reportName: 'HTML Report', 
                 reportTitles: 'Code Coverage-Report'])

    archiveArtifacts 'target/*.war' 
    step([$class: 'Mailer', 
        notifyEveryUnstableBuild: true, 
        recipients: 'cc:shriniwm1969@gmail.com', 
        sendToIndividuals: false])
 }
 notify ('Waiting for Deployment')
 
 input 'Deploy to Staging?'
 
 stage ('Deploy to AppServer') {
 sh 'cp target/petclinic.war /opt/apache-tomcat-8.5.24/webapps'
 sh 'sudo /opt/apache-tomcat-8.5.24/bin/shutdown.sh'
 sh 'sudo /opt/apache-tomcat-8.5.24/bin/startup.sh'
 }
 
  notify('Completed')  
}

 def notify(status) {
  mail (
        body:"""${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
                 Check console output at, 
                 href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]""",
        cc: 'shriniwm1969@gmail.com', 
        subject: """JenkinsNotification: ${status}:""", 
        to: 'shriniwm1969@gmail.com'  
       ) 
 }