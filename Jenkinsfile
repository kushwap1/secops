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
                  sshCommand remote: remote, failOnError: false, sudo: true, command: 'if [ `inspec | echo $? -ne 0` ]; then curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -P inspec; sudo -i; cd /root; git clone https://github.com/dev-sec/linux-baseline.git; else echo "Inspec already installed"; sudo -i; cd /root; git clone https://github.com/dev-sec/linux-baseline.git; fi'
              }
                stage("Scan with InSpec") {
                  sshCommand remote: remote, failOnError: false, sudo: true, command: 'inspec exec /root/linux-baseline/'
            
              }
              stage("Install Ansible") {
                  sshCommand remote: remote, failOnError: false, sudo: true, command: 'if [ `ansible | echo $? -ne 0` ]; then apt-get install ansible; echo "Ansible Installed successfully"; else echo "Ansible already installed"; fi'

              }
            }
          }
       }
    }
  }
}
