pipeline {
  agent any

  stages {
    stage('Daily Compliance Run') {
      steps{
        echo 'Running a compliance scan with inspec....'
          script{
            def remote = [:]
            remote.name = "instance-416"
            remote.host = "10.160.0.3"
            remote.allowAnyHosts = true

            withCredentials([sshUserPrivateKey(credentialsId: 'sshUser', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
                remote.user = userName
                remote.identityFile = identity
                stage("Install InSpec complianc and Linux baseline") {
                  sshCommand remote: remote, sudo: true, command: 'inspec && if [ $? -eq 0 ]; then echo "Inspec already installed"; else curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -P inspec; fi'
                  sshCommand remote: remote, sudo: true, command: 'sudo -i && cd /root && git clone https://github.com/dev-sec/linux-baseline.git'
              }
                stage("Scan with InSpec") {
                  sshCommand remote: remote, sudo: true, command: 'inspec exec /root/linux-baseline/'
              }
            }
          }
       }
    }
  }
}
