# Simple SSH Bastion for Kubernetes 


This is simple helm chart that will allow you to install ssh bastion inside your cluster.
Bastion is based on clean alpine.

To configure your bastion use values e.g:

```
bastion:
  #This will be single user to which you will be adding ssh keys.
  sshUser: bastion
  #here is your ssh keys.
  sshKeys:
    - "ssh-ed25519 ADD_YOUR_KEY_HERE yourname@yourhost"
  #if you need to change any configuration to your ssh.
  sshd_config:
    TCPKeepAlive: "yes"
    ClientAliveInterval: 600
    ClientAliveCountMax: 3
    PermitRootLogin: "no"
    PasswordAuthentication: "no"

service:
  type: LoadBalancer
  port: 22
  annotations:
    # with usage of external dns you can assign 
    external-dns.alpha.kubernetes.io/hostname: bastion.example.com.
    # if you are using nlb loadbalancer you will need to increase connection idle timeout.
    service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout: "4000"
  #use this if you need whitelisting. This value depends on your loadBalancer type. Generally it works good with nlb in AWS.
  loadBalancerSourceRanges: 
    - "1.1.1.1/32"
```
