---
# Basic information
This is a quick and dirty Ansible task that I use to deploy FTP server on LXC containers.  

If behind NAT, you will need to forward ports ```21``` (or whatever you use for FTP access), ```passive_ftp_min_port``` and ```passive_ftp_max_port``` to the FTP instance.

## Netfilter rules with ```10.0.3.207``` being my FTP container internal address
```
# test.com
$IPTABLES -A PREROUTING -t nat -p tcp --dport 21 -j DNAT --to 10.0.3.207:21
$IPTABLES -A POSTROUTING -t nat -p tcp -d 10.0.3.207 -s 10.0.3.1/255.255.255.0 --dport 21 -j SNAT --to 10.0.3.1
$IPTABLES -t nat -A PREROUTING -p tcp --dport 15000:20000 -j DNAT --to 10.0.3.207
```

## Variables to adjust, with examples

### Username for FTP access.
client_username=dev-ftp 
### Passive ports configuration.
passive_ftp_min_port=15000  
passive_ftp_max_port=20000  
### If you want SSL support, you have to install a certificate. For faster testing I have disabled SSL by default.
ssl_ftp=no
### This should be your piblic IP address.
ftp_address=192.168.10.5

## How to use it?
Run it with ```ansible-playbook -l test_com install-ftp-playbook.yml```

This is my example inventory file:
```
[test.com]
test_com ansible_host=10.0.3.207

[test.com:vars]
client_username=dev-ftp
passive_ftp_min_port=15000
passive_ftp_max_port=20000
ssl_ftp=no
ftp_address=192.168.10.5
```