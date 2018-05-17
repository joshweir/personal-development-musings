# Leadership / Teamwork / Personal Musings
Notes related to developing my leadership, teamwork and personal life.

## AWS

### Security
* Don't create an access key for the root account.
* Don't access AWS using the root account, create a new IAM user. Use MFA for users.
* Don't assign permissions to a user, create a group for permissions, assign users to a group. 
* EC2 applications require IAM roles to access other AWS services / instances, the EC2 instance is launched with a specified role - applications inherit this role. 
* Roles are also used to allow other users to access resources in your AWS account.
