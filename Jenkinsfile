node('master') 

{ 

    

   stage('SCM Checkout') 

             { 

                  git 'https://github.com/mythribhat/GeneralSpringBootProgExce.git' 

              } 

        

   stage('Build')  

         { 

                  withSonarQubeEnv('sonar')  

                  { 

                        sh '/opt/maven/bin/mvn clean verify sonar:sonar -Dsonar.password=admin -Dsonar.login=admin' 

                  } // SonarQube taskId is automatically attached to the pipeline context 

         } 

   

   stage("QG Check") 

         { 

                  timeout(time: 1, unit: 'HOURS')  

                  { // Just in case something goes wrong, pipeline will be killed after a timeout 

                        def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv 

                        if (qg.status != 'OK')  

                        { 

                            error "Pipeline aborted due to quality gate failure: ${qg.status}" 

                        } 

                  } 

         } 

    

      stage('SIT – Deployment Approval') 

        { 

             sh '/opt/maven/bin/mvn clean deploy -DaltDeploymentRepository=internal.repo::default::http://admin:admin123@18.222.187.3:8081/nexus/content/repositories/snapshots/' 

         } 
    
    post {
        always {
            emailext body: 'A Test EMail', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
        }
    }
  

} 

stage('Deploy approval'){
    input "Deploy to prod?"
}



