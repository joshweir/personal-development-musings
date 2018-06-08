# AWS Notes

Understand the difference between a region and availability zone (AZ) and an edge location.
* Region: geographical region
* AZ: Essentially data centre, could be many in a region.
* Edge location: more than AZ, used for cache.

Wholistically be able to identify all services (briefly) - *TODO: go over the 10,000ft overview again and take notes*

## IAM

Users, Groups (group of users to apply policies) 
Roles: allow you to grant permissions to application code in ec2, aws services, other user accounts. 
Policy Documents: in json, show permissions, policies can be associated to users, groups or roles.

IAM sits accross all regions hence the "Global" in the top right
New Users have NO permissions when first created

Login to root account rarely 

The Access Key ID / secret is only for programatic access, you can't login to console with it, you cant use normal username and password for programatic access.
Always apply MFA to root account
create password rotation policies

## Lambda

important to know what all the triggers could be.
for example, using api gateway, user or services trigger http request and this spawns a new lambda function for each http request, this way scale is automatically achieved.
lambdas are pay by the lambda, first million free
available runtime (need to check if this is now updated): c#, java, python, node.js

how would i implement a node express server running - check that.
and how say would i serve a react redux app over lambda

lambda scales out (not up) automatically
lambda is serverless
lambda functions can trigger other lambda functions
max 5 minutes run time, trigger another if need to be longer
