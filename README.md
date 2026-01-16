# SSH_Passwordless_Authentication
LAN network and within same computer windows and virtual kali-linux

-> What is the project for 
 The project mainly explains about logging in using SSH without typing a password
 Using SSH(22) protocol service we can operate a computer from another computer
 This project is only for understanding basic fundamentals 
 How the things will behave

 -> Overview
  We traditionally can login into another system terminal using password by SSH,
  But it is not fully secure mean there are different ways to crack passowrds like bruteforce
  here, We use SSH keys private and public key these two ecoded cryptographic keys made basis of some
  algorithms for example # *type* **ed25519** 

  In this we login by keys and debug general errors and real mistakes.

  ->What is main Objective?
  
   Replace password explained   
            # passwords are guessable
            # keys are safer
   Understandable process
            # private key = proves identity
            # public key = permission token
   Understand file **authorized_keys**
            # ~/home/user/.ssh/authorized_keys
            # it is heart of ssh
            # ssh believes only this file
   Understand permissions
            # give only permissons to user 700
            # SSH rejects request if permissions are unsafe

 -> Technologies Used
     1. Linux (Openssh server)  - virtualbox in same computer
     2. Windows (Openssh client) - Initiates SSH login
     3. ED25519 SSH Keys - modern cryptographic algorithm safer faster

 -> Core Concepts Explained
   1. Private Key and Public Key
                    SSH uses two related key instead of a password, The private
      key is secret and stays only on the client machine. It proves that the user is really who they claim to be. 
      The public key is not secret and it is copied to the server. The server uses the public key to identify /verify
      /check whether the client really owns the private key right. If private key leaked or shared any one can log in,
      soo it must always be protected.
      
   2. Role of `authorized_keys`
                      SSH does not trust public keys automatically. The server allows login if the public key is written in
       specific file named 'authorized_keys'. This file acts like an official permission list for the user. Even if a public
       key file exists in the folder, SSH will ignore it unless it is placed inside 'authorized_keys'
   4. Permission Configuration
                       SSH is very strict about security. If the '.ssh' directory or the 'authorized_keys' file has open permissions,
       SSH assumes that another could modify them. To prevent this risk, SSH silently rejects the key and asks for the password instead.
       Correct permissions are required for SSH to accept the key.
   6. Clent and Server Responsibility
                       In SSH, the client and server have different roles. Thee client initiates the connection and proves it's identity
      by private key. The server checks private key correct or not using public key in 'authorized_keys'. Guys confusing client side and
      server side mistakes are beginner mistakes.
                       
                        
>> Implementation Steps

 Step 1: SSH Key Generation
An SSH key pair was generated using the ED25519 algorithm. This created a private key and a public key. The private key was kept securely 
on the client system, and the public key was prepared to be copied to the server.



 Step 2: Public Key Authorization
The public key was copied to the server and pasted inside the `authorized_keys` file located in the user’s `.ssh` directory. This step
explicitly informed the server that this key is trusted for login.



 Step 3: Permission Configuration
File ownership and permissions were corrected to meet SSH security requirements. The `.ssh` directory was restricted to the user, and the 
`authorized_keys` file was protected so that no other user could read or modify it.



 Step 4: Testing Passwordless Login
After configuration, SSH login was tested from the client machine. The system allowed access without requesting a password, confirming that
key-based authentication was working correctly.



>> Errors Faced and Their Solutions

 Error 1: Public Key Exists but Login Failed
Initially, the public key file existed in the `.ssh` directory, but SSH still asked for a password. This happened because SSH does not read 
`.pub` files directly. The issue was resolved by adding the public key to the `authorized_keys` file.



 Error 2: SSH Still Asked for Password After Authorization
Even after adding the key to `authorized_keys`, SSH continued asking for a password. The cause was incorrect file permissions, which SSH
considered insecure. After fixing the permissions, SSH accepted the key.


 Security Best Practices Followed
The private key was never shared or uploaded anywhere. The server stored only the public key, not the private key. Strict file permissions were
enforced to prevent unauthorized access. Password-based authentication was avoided in favor of secure key-based login.



 Verification and Debugging
Passwordless login was verified by connecting from the client to the server using the private key. When issues occurred, verbose SSH logging was 
used to understand exactly where authentication failed.



 Project Level
This project is classified as Beginner to Intermediate. It focuses on understanding SSH fundamentals, security rules, and real-world troubleshooting
rather than advanced automation or enterprise setups.



 Key Learning Outcome
SSH passwordless authentication works only when the server explicitly trusts a public key through the `authorized_keys` file and when all security 
conditions such as ownership and permissions are correctly followed. Simply generating keys is not sufficient.



 Future Improvements
This project can be extended by disabling password authentication completely, adding protection against brute-force attacks, restricting SSH access
using firewall rules, or integrating VPN-based access for higher security.



SSH Passwordless Login Guide

GOAL: Login to a Linux server from Windows using SSH keys instead of a password.
Basic Concept

SSH uses a key-pair system:

    Private Key (pk): Stored on the CLIENT (Windows). This is your secret identity.

    Public Key (pk.pub): Stored on the SERVER (Linux). This goes into the authorized list.

Rule: SSH allows login only if the Public Key is inside the authorized_keys file on the server and file permissions are strictly set.
Step 1: Generate SSH Key (On Linux)

Run the following command to create a secure key pair:
Bash

ssh-keygen -t ed25519 -f pk

Files created:

    pk : Private Key (DO NOT SHARE)

    pk.pub : Public Key (SAFE TO SHARE)

Step 2: Decide Roles

    CLIENT: Windows machine (Needs the Private Key)

    SERVER: Linux machine (Needs the Public Key)

Step 3: Copy Private Key to Windows (Client)

Move the pk file from the Linux server to your Windows machine.

    Destination: C:\Users\Raghu\.ssh\pk

    IMPORTANT: Never upload the private key to social media or leave it exposed on the server.

Step 4: Prepare SSH Directory on Server

On the Linux server, ensure the .ssh folder exists:
Bash

cd ~
mkdir -p ~/.ssh

Step 5: Authorize Public Key

You must add the contents of the public key to the server's authorization list.

    Open or create the file:
    Bash

    nano ~/.ssh/authorized_keys

    Open pk.pub, copy the text, and paste it into authorized_keys.

        It should look like: ssh-ed25519 AAAAC3NzaC1lZDI1NTE5... user@host

    Save and exit.

    NOTE: Saving the .pub file separately does not work. The content must be inside authorized_keys.

Step 6: Fix Ownership and Permissions (CRITICAL)

SSH will silently reject keys if permissions are too open. Run these commands exactly:
Bash

# Ensure correct ownership
chown -R user_name:group_name ~/.ssh

# Directory permissions (User read/write/execute only)
chmod 700 ~/.ssh

# File permissions (User read/write only)
chmod 600 ~/.ssh/authorized_keys

Step 7: Cleanup

Remove the generated key files from the server (since the private key is now on Windows).
Bash

rm -f pk pk.pub

Step 8: Check Server Config (Optional)

If it still doesn't work, check the configuration file: sudo nano /etc/ssh/sshd_config

Ensure these lines exist and are uncommented:

    PubkeyAuthentication yes

    AuthorizedKeysFile .ssh/authorized_keys

Restart SSH if you made changes: sudo systemctl restart ssh
Step 9: Login Test from Windows

Run this command from PowerShell or CMD:
Bash

ssh -i C:\Users\Raghu\.ssh\pk jasprit93@SERVER_IP

Expected Result: You log in immediately without a password prompt.
Step 10: Debug if Login Fails

If you are asked for a password, run the command with -vvv (verbose mode) to see the logs:
Bash

ssh -vvv -i C:\Users\Raghu\.ssh\pk jasprit93@SERVER_IP

Log Interpretation:

    "Offering public key" -> The client is sending the key correctly.

    Password prompt appears -> The server rejected the key (usually a permission issue on Step 6).

Common Errors and Fixes

Error 1: Public key exists but login asks for password

    Reason: Public key content is not inside authorized_keys.

    Fix: Copy content of pk.pub into ~/.ssh/authorized_keys.

Error 2: authorized_keys permission is 664 or 777

    Reason: SSH considers this unsafe and ignores the file.

    Fix: Run chmod 600 ~/.ssh/authorized_keys.

Error 3: Files owned by root

    Reason: SSH does not trust files not owned by the user.

    Fix: Run chown -R username:username ~/.ssh.

Security Rules

    Never share the Private Key.

    Server must store only Public Keys.

    Use key-based login instead of passwords whenever possible.

One-Line Summary

SSH login without a password works only when the server explicitly trusts a public key through authorized_keys with correct ownership and strict permissions.




CONCLUSION: This may be developed and updated soon........................................................................
