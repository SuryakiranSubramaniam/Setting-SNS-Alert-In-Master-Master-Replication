# Setting-SNS-Alert-In-Master-Master-Replication
Setting-SNS-Alert-In-Master-Master-Replication

## Create topic
![alt text](https://github.com/SuryakiranSubramaniam/Setting-SNS-Alert-In-Master-Master-Replication/blob/main/image/Topic-1.png)
![alt text](https://github.com/SuryakiranSubramaniam/Setting-SNS-Alert-In-Master-Master-Replication/blob/main/image/Topic-2.png)
## Create subscription
![alt text](https://github.com/SuryakiranSubramaniam/Setting-SNS-Alert-In-Master-Master-Replication/blob/main/image/Subscription-1.png)
![alt text](https://github.com/SuryakiranSubramaniam/Setting-SNS-Alert-In-Master-Master-Replication/blob/main/image/subscription-2.png)
## AWS Notification - Subscription Confirmation
![alt text](https://github.com/SuryakiranSubramaniam/Setting-SNS-Alert-In-Master-Master-Replication/blob/main/image/confirmation.png)
## Install and configure AWS CLI

#### Install AWS CLI

> ~]# pip3 install awscli

#### Configure AWS CLI

```
[root@ip-172-31-41-132 ~]# aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: ap-south-1
Default output format [None]: 
```
## Create Shell script

```
~]# cat script.sh 
#!/bin/bash

SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

UP=$(service mariadb status | grep running | grep -v not | wc -l);
host=`hostname`
msg="Mysql service is down on the server ${host}"
if [ "$UP" -ne 1 ];
then
        aws sns publish --topic-arn arn:aws:sns:ap-south-1:323568642936:Master2Master --message "${msg}";
        sudo service mariadb start
        aws sns publish --topic-arn arn:aws:sns:ap-south-1:323568642936:Master2Master --message "Mysql started again now!";
else
        echo "All is well.";
fi
```
> chmod +x script.sh

```
~]# crontab -e

~]# crontab -l
* * * * *	/root/script.sh
```
```
[root@ip-172-31-41-132 ~]# systemctl stop mariadb.service 
[root@ip-172-31-41-132 ~]# service mariadb status | grep running | grep -v not | wc -l
Redirecting to /bin/systemctl status mariadb.service
0
[root@ip-172-31-41-132 ~]# service mariadb status | grep running | grep -v not | wc -l
Redirecting to /bin/systemctl status mariadb.service
0
You have new mail in /var/spool/mail/root
```
### AWS Notification Message

```
[root@ip-172-31-41-132 ~]# service mariadb status | grep running | grep -v not | wc -l
Redirecting to /bin/systemctl status mariadb.service
1

```
![alt text](https://github.com/SuryakiranSubramaniam/Setting-SNS-Alert-In-Master-Master-Replication/blob/main/image/notification.png)
