## Hello World
```python
import boto3  
'''  
pip3 install boto3  
  
Configure ~/.aws/profile  
[profile <profile_name>]  
region=us-west-2  
  
Configure ~/.aws/credentials  
[<profile_name>]  
credential_process=/Users/jnesztr/.toolbox/bin/ada credentials print --account=973233092256 --provider=conduit --role=IibsAdminAccess-DO-NOT-DELETE  
  
Sim for longer credential time: https://issues.amazon.com/issues/ADA-4655  
  
OR  
  
Create a user to assume in IAM console.  
  
Download access keys.  
  
Create a role that the user can assume.  
  
Configure ~/.aws/profile  
[profile <profile_name>]  
region=us-west-2  
  
Configure ~/.aws/credentials  
[<profile_name>]  
aws_access_key_id=<aws_access_key_id>  
aws_secret_access_key=<aws_secret_access_key>  
'''  
# Temp ADA creds  
# session = boto3.Session(profile_name='artdedup-beta')  
# Use AWS User  
session = boto3.Session(profile_name='artdedup-user-jnesztr-sdk')  
# print(f'Temp session started. Pick a service: {session.get_available_services()}')  
cwl = session.client('logs')  
print(cwl.describe_log_groups())
```