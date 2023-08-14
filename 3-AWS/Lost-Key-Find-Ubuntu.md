### In case EC2 private key is lost 
 
Suppose your "lost.pem" file is lost.

- Now create a new key "find.pem"
- Copy this "find.pen" file on EC2 machine under any folder (for example /opt) using instant connect.
```
sudo nano find.pem 
<Copy and paste the content of find.pem file from your download folder to this nano editor>
```
```
sudo chmod 400 find.pem
sudo ssh-keygen -y -f find.pem 
<Press enter>
```
A key will be generated like below:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCeKZhxoHSb0/JGxvoOtn2mWHsCfQ39bdLpnsO7v02oc+1jxTt6Xl61JsdmTsWKjAKMDwFejLkYFPRfE0e2+qqW+jKSxh/loOEGgP56TEgyw5tX1b/eq7PAVPzOrpI4E383ml9qJFxfWDXJSomHZQ4NvN4tnuP0QCV5RPjleAVfw2fWg5ASeTrNrGAWlVfXdUNbvR5n8OYrs7BGNDM3shQopCoHmOqJ3yoNOSEku34sTZntQw6gsdekGBVGrMtftxTuUaLWW/JQvSpKEuNQyhzv8exsK3dkoQqn9n0b8kW0CkWpSpIpGbB5IvLfypidP+maHje6aYizrm0IRamiwc3H
```
- copy this key into "/home/ubuntu/.ssh/authorized_keys" file

- Now you can login to the instance using new "find.pem" key.
