void init_ssh(String ssh_key)
{
    echo "Configure ssh"
    sh """
        mkdir -p ~/.ssh
        cat ${ssh_key} > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa
        echo "Host *" > ~/.ssh/config
        echo "    StrictHostKeyChecking no" >>  ~/.ssh/config
    """
}

pipeline {
  agent {
      label 'slave'
  }
  stages {
    stage('Building image') {
      steps{
        script {
          sh "mvn package"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {        
        withCredentials([usernamePassword(credentialsId: 'deployer', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASSWORD')]) {
        sh """
          CURL_RESPONSE=\$(curl -v -u $TOMCAT_USER:$TOMCAT_PASSWORD -T target/*.war "http://35.204.214.31:80/manager/text/deploy?path=/helloo&update=true")    
          if [[ \$CURL_RESPONSE == *"FAIL"* ]]; then
            echo "war deployment failed"
            exit 1
          else    
            echo "war deployed successfully "
            exit 0
          fi
        """
          }
        }
      }
    }
  }
}
