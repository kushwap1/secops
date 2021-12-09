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
                  writeFile file: 'secops.sh', text: 'if [ `inspec | echo $? -ne 0` ]; then curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -P inspec; mkdir ~/folder1; sleep 5; git clone https://github.com/dev-sec/linux-baseline.git ~/folder1; else echo "Inspec already installed"; mkdir ~/folder1; sleep 5; git clone https://github.com/dev-sec/linux-baseline.git ~/folder1; inspec exec ~/folder1/linux-baseline/; fi'
                  sshScript remote: remote, failOnError: false, script: "secops.sh"
              }
              stage("Install Ansible") {
                  writeFile file: 'ansible.sh', text: 'if [ `ansible | echo $? -ne 0` ]; then apt-get install ansible; echo 'Ansible Installed successfully'; else echo "Ansible already installed"; fi'
                  sshScript remote: remote, failOnError: false, script: "ansible.sh"
              }
            }
          }
       }
    }
  }
}
