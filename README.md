# multi-docker-aws

## Deploy setup to AWS

### EBS Application Creation

Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

1. Click “Create Application”
2. Set Application Name to 'multi-docker'
3. Scroll down to Platform and select Docker
4. In Platform Branch, select Multi-Container Docker running on 64bit Amazon Linux
5. Click Create Application
6. You may need to refresh, but eventually, you should see a green checkmark underneath Health.
    
### RDS Database Creation

Go to AWS Management Console and use Find Services to search for RDS

1. Click Create database button
2. Select PostgreSQL
3. In Templates, check the Free tier box.
4. Scroll down to Settings.
5. Set DB Instance identifier to multi-docker-postgres
6. Set Master Username to postgres
7. Set Master Password to "your password" and confirm.
8. Scroll down to Connectivity. Make sure VPC is set to Default VPC
9. Scroll down to Additional Configuration and click to unhide.
10. Set Initial database name to fibvalues
- Scroll down and click Create Database button

### ElastiCache Redis Creation

Go to AWS Management Console and use Find Services to search for ElastiCache

1. Click Redis in sidebar
2. Click the Create button
3. Make sure Cluster Mode Enabled is NOT ticked
4. In Redis Settings form, set Name to multi-docker-redis
5. Change Node type to 'cache.t2.micro'
6. Change Replicas per Shard to 0
7. Scroll down and click Create button

### Creating a Custom Security Group

Go to AWS Management Console and use Find Services to search for VPC

1. Find the Security section in the left sidebar and click Security Groups
2. Click Create Security Group button
3. Set Security group name to multi-docker
4. Set Description to multi-docker
5. Make sure VPC is set to default VPC
6. Click Create Button
7. Scroll down and click Inbound Rules
8. Click Edit Rules button
9. Click Add Rule
10. Set Port Range to 5432-6379
11. Click in the box next to Source and start typing 'sg' into the box. Select the Security Group you just created.
12. Click Create Security Group

### Applying Security Groups to ElastiCache

Go to AWS Management Console and use Find Services to search for ElastiCache

1. Click Redis in Sidebar
2. Check the box next to Redis cluster
3. Click Actions and click Modify
4. Click the pencil icon to edit the VPC Security group. Tick the box next to the new multi-docker group and click Save
5. Click Modify


### Applying Security Groups to RDS

Go to AWS Management Console and use Find Services to search for RDS

1. Click Databases in Sidebar and check the box next to your instance
2. Click Modify button
3. Scroll down to Network and Security and add the new multi-docker security group
4. Scroll down and click Continue button
5. Click Modify DB instance button

### Applying Security Groups to Elastic Beanstalk

Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

1. Click Environments in the left sidebar.
2. Click MultiDocker-env
3. Click Configuration
4. In the Instances row, click the Edit button.
5. Scroll down to EC2 Security Groups and tick box next to multi-docker
6. Click Apply and Click Confirm
7. After all the instances restart and go from No Data to Severe, you should see a green checkmark under Health.

### Setting Environment Variables

Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

1. Click Environments in the left sidebar.
2. Click MultiDocker-env
3. Click Configuration
4. In the Software row, click the Edit button
5. Scroll down to Environment properties
6. In another tab Open up ElastiCache, click Redis and check the box next to your cluster. Find the Primary Endpoint and copy that value but omit the :6379
7. Set REDIS_HOST key to the primary endpoint listed above, remember to omit :6379
8. Set REDIS_PORT to 6379
9. Set PGUSER to postgres
10. Set PGPASSWORD to "your password"
12. In another tab, open up the RDS dashboard, click databases in the sidebar, click your instance and scroll to Connectivity and Security. Copy the endpoint.
13. Set the PGHOST key to the endpoint value listed above.
14. Set PGDATABASE to fibvalues
15. Set PGPORT to 5432
16. Click Apply button
17. After all instances restart and go from No Data, to Severe, you should see a green checkmark under Health.

### IAM Keys for Deployment

Go to AWS Management Console and use Find Services to search for IAM

1. Click Users in the left sidebar.
2. Click Add User
3. Set User name to multi-docker-deployer
4. In Access Type select Programmatic access
5. Click Next Permission button
6. Select Attach existing policies directly 
7. Search for "beanstalk"
8. Check "AWSElasticBeanstalkFullAccess" option
9. Click Next Tags button
10. Click Next Review button
11. Click Create User button

### AWS Keys in Travis

Go to your Travis Dashboard and find the project repository for the application we are working on.

1. On the repository page, click "More Options" and then "Settings"
2. Create an AWS_ACCESS_KEY variable and paste your IAM access key
3. Create an AWS_SECRET_KEY variable and paste your IAM secret key

### AWS buckt in Travis

Go to AWS Management Console and use Find Services to search for S3

1. Create a bucket for your region

