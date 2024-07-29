https://devops4solutions.medium.com/setup-ssh-key-and-initial-user-using-ansible-playbook-61eabbb0dba4

Checkout my blog of using automation of setting up an initial user [https://devops4solutions.com/automate-ansible-playbook-deployment-on-aws-ec2/](https://devops4solutions.com/automate-ansible-playbook-deployment-on-aws-ec2/)

In this blog we will Setup SSH Key and initial user using Ansible Playbook

To create new user on ubuntu system, you need the following things:

1. Username/Password
2. Public Key of the user
3. You will ==first== create a user on one machine. Machine can be your local workstation also
4. Generate ssh-key for this
5. Put the public key of that user to the remote hosts.
6. Add that user to the sudoers.d file

**Steps:**

sudo -i  
_useradd -m -s /bin/bash devops  
passwd devops  
echo -e ‘devops\tALL=(ALL)\tNOPASSWD:\tALL’ > /etc/sudoers.d/devops_Encrypt your password  
_sudo apt install whois -y__mkpasswd — method=SHA-512  
TYPE THE PASSWORD ‘devops’_

![](https://miro.medium.com/v2/resize:fit:700/1*d3fspoQao1ntxgvDMRlLQA.png)

**Generate a new SSH-key**

1. Login as a devops user

_ssh-keygen -t rsa_

It will generate the public and private key file for the devops user.

![](https://miro.medium.com/v2/resize:fit:700/1*TEGm8jDeUt7WqbyT5TWMJw.png)

Now we have to add this public key to all the remote hosts.

**Create Ansible playbook “add-user-ssh.yml”**

- Add a devops user
- Now we want to disable the Password Authentication on all the remote hosts.This means no user/root user can login to the system by using password. They have to use the SSH keys only.

---  
 - hosts: all  
   vars:  
     - devops_password: 'abcddefsfdfdfdfdfdfdfdfdfdfd'  
   gather_facts: no  
   remote_user: ubuntu  
   become: truetasks:- name: Add a new user named devops  
     user:  
          name: devops  
          shell: /bin/bash  
          password: "{{ devops_password }}"- name: Add devops user to the sudoers  
     copy:  
          dest: "/etc/sudoers.d/devops"  
          content: "devops  ALL=(ALL)  NOPASSWD: ALL"- name: Deploy SSH Key  
     authorized_key: user=devops  
                     key="{{ lookup('file', '/home/devops/.ssh/id_rsa.pub') }}"  
                     state=present- name: Disable Password Authentication  
     lineinfile:  
           dest=/etc/ssh/sshd_config  
           regexp='^PasswordAuthentication'  
           line="PasswordAuthentication no"  
           state=present  
           backup=yes_- name: Disable Root Login  
     lineinfile:  
           dest=/etc/ssh/sshd_config  
           regexp='^PermitRootLogin'  
           line="PermitRootLogin no"  
           state=present  
           backup=yes  
     notify:  
       - restart ssh_handlers:  
   - name: restart ssh  
     service:  
       name=sshd  
       state=restarted  

**Run the playbook**

ansible-playbook **add-user-ssh**.yml -i hosts

**Validate Disable Password Authentication**

$ ssh servername -o PubkeyAuthentication=no

You will get the “Permission Denied(public key)