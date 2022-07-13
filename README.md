# General SSH cfg

   
- if setuped ssh go to remote client and opend cfg

      nano /etc/ssh/sshd_config

rewrite this:

  UsePAM no
  PasswordAuthentication no

- save 

        reboot  

# Chek problem login ssh

- Any Client can't connect with ssh. Open the remote server
        
      cd /var/log# tail -f auth.log
      tail -f auth.log

- and now try connect to server with client, cheking log and see the problem

# Set SSH irectory permissions

    chmod 700 ~/.ssh

# Set public/private key permissions

    chmod 600 ~/.ssh/id_rsa
    chmod 600 ~/.ssh/id_rsa.pub

- or other name key

