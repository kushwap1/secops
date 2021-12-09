pipeline {
  agent any

  stages {
    stage('Daily Compliance Run') {
      steps{
        echo 'Running a compliance scan with inspec....'
          script{
            def remote = [:]
            remote.name = "instance-416"
            remote.host = "10.160.0.4"
            remote.allowAnyHosts = true

            withCredentials([sshUserPrivateKey(credentialsId: 'sshUser', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
                remote.user = userName
                remote.identityFile = identity
                stage("Install InSpec complianc and Linux baseline") {
                  sshScript remote: remote, failOnError: false, script: "secops.sh"
              }
                stage("Scan with InSpec") {
                  sshCommand remote: remote, failOnError: false, sudo: true, command: 'inspec exec /home/cloud_user/folder1/linux-baseline/ && rm -rf /home/cloud_user/folder1'
            
              }
              stage("Install Ansible") {
                  sshScript remote: remote, failOnError: false, script: "ansible.sh"
              }
            }
          }
       }
    }
  }
}
