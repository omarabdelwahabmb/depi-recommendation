Requirements:
Provision at least t2.micro EC2 Instance on AWS
Save the private key in secure path
chmod 400 key.pem

Download Jenkins jar from the link below:
Link*******************************************

Install the mail plugin and configure it
Install the email extension plugin

Go to manage Jenkins > system 
  Scroll down to Email Notification
  Under SMTP Server
    smtp.gmail.com
  Under Advanced
    Check Use SMTP Notification
      Enter UserName
    Check SSL, TLS
    SMTP Port
    465

Go to Google Account Security Settings
  2 step verification
  search for app passwords
  App passwords
  Create a new one for Jenkins
  copy the password
  
Paste it in Jenkins then test mail configuration

  Save

Add Docker USERNAME and PASSWORD for docker credentials



Create a new pipeline
choose github project
add the following url:
https://github.com/omarabdelwahabmb/depi-recommendation

Scroll down and choose pipeline from SCM.

re-enter the following url:
https://github.com/omarabdelwahabmb/depi-recommendation

change Branch Specifier and replace */master with */main

click save
run the pipeline

It will fail for first run, try again

Enter the required fields