# neccdc2018info

This repository is the collaboration to update the current http://neccdc.net site.

The site will provide information for competitors(students), sponsors and volunteers.

Instructions to standing up the site in AWS

# python to create AWS instance and setup webserver
import boto3
session = boto3.Session(profile_name='default', region_name='us-east-1')
ec2 = session.client('ec2')

# Setup the instance and clone the repo
userData = '#!/bin/bash\nyum -y update\nyum -y install git nginx\n' \
    'service nginx start\nchkconfig nginx on\n' \
    'git clone https://github.com/kengraf/neccdc2018info' \
    'mv -f /neccdc2018info/* /usr/share/nginx/html'

# Set the following for your specific account and usage
ami = {'name':'Amazon Linux AMI 2017.03.0', 'id':'ami-c58c1dd3'}
keyName="NECCDC"
secgroup = {'id':'sg-3da1e643', 'name':'World-HTTP_UNH-SSH'}
tags = [{
            'ResourceType': 'instance',
            'Tags': [
                {
                    'Key': 'Name',
                    'Value': 'neccdc2018.org'
                }
            ]
        }]

response = ec2.run_instances(ImageId=ami['id'], MinCount=1, MaxCount=1,
                             KeyName='NECCDC',
                             InstanceType='t2.micro',
                             UserData=userData,
                             TagSpecifications=tags,
                             SecurityGroupIds=[secgroup['id']])