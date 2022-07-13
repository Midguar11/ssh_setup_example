# General SSH cfg

   
- if setuped ssh go to remote client and opend cfg

      nano /etc/ssh/sshd_config

rewrite this:

  UsePAM no
  PasswordAuthentication no

- save 

      reboot  

- windows ssh dierctory: \Users\user\.ssh


# Checking problem to login with ssh

- Any Client can't connect with ssh. Open the remote server
        
      cd /var/log
      tail -f auth.log

- and now try connect to server with client, cheking log and see the problem

# Set SSH irectory permissions

    chmod 700 ~/.ssh

# Set public/private key permissions

    chmod 600 ~/.ssh/id_rsa
    chmod 600 ~/.ssh/id_rsa.pub

- or other name key

# Jenkins connect with ssh to Kubernetes

- First open jenkins manager install plugin : " ssh pipeline step "
- Login to ssh in Jenkins server

      cd .ssh
      ssh-keygen -m PEM
      ssh-copy-id -i generatedkey user@kubermasterip

- or connect ssh KubeMaster:
- from jenkins server cat generated.pub key copy and pastle to here

      sudo nano .ssh/authorized_keys

- authentication config, connect KubeMaster editing file:

      sudo nano /etc/ssh/sshd_config

- Add this:

        PubkeyAuthentication yes
        PubkeyAcceptedKeyTypes=+ssh-rsa

- change " PasswordAuthentication no"

save and reboot

- Jenkins manager add new credentials: Kind " ssh username with private key. Add usermname, desrciption and id. 
- Privatet key enter directly pastle here jenkins server generated private key.

- use similar code in a pipeline:

 stage("SSH Into Kubernetes Server with secret key") {
        def remote = [:]
        remote.name = 'KubeMaster'
        remote.host = '192.168.0.111'
        remote.allowAnyHosts = true
        
        node {
            withCredentials([sshUserPrivateKey(credentialsId: 'KubeMasterSSHKeyPem', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
                remote.user = userName
                remote.identityFile = identity
                
                stage('Put deploymentservice.yaml into KubeMaster') {
                    sshPut remote: remote, from: '/var/lib/jenkins/workspace/Practice/Complet_pipeline_kubernetes_sshSecret/deploymentservice.yaml', into: '.'
                }
                
                stage('Deploy spring boot') {
                  sshCommand remote: remote, command: 'kubectl apply -f deploymentservice.yaml'
                }
                
            }
        }
    }
