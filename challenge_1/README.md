# CHALLENGE 1

![3 tier architecture](https://github.com/foggy-glasses/kpmg-technical-challenge-answer/blob/main/challenge_1/aws-architeecture-3-tier-example.png "AWS 3 Tier Architecture")

The 3 tier architecture diagram is found in the same folder (aws-architeecture-3-tier-example.png). This is taken from AWS documentation https://docs.aws.amazon.com/managedservices/latest/appguide/cfn-ingest-ex-3-tier.html

The are 3 main components for this architecture viz.
- Frontend
- Application
- Backeend

### Assumptions made
- The servers would be running Ubuntu 20.04
- Server sizing will depend on the type of application running on it.
- For simplicity sake, the application layer would consist of one or more Java applications running on OpenJDK.
- The servers will be part of an autoscaling group that would scale up when the CPU utilization crosses 80% for 10 minutes
- The Database used is MySQL v5.27
- The entire cost for this architecture can be optimized by either Compute Savings Plan / Savings Plan / Reserved Instances.

To facilitate High Availability and Fault Tolerance, the deployment requires the following.
- A VPC consisting of public and private subnets spread across 2 availability zones
- 2 DB subnets exclusively for the backend RDS instances
- A bastion server on the public subnet to acceess the servers on the private subnet.
- A Load Balancer on the public subnet with its target group set to the application servers.

### Frontend
- The AWS Loadbalancer will proxy the requests to the servers behind it (preferably in a round robin fashion for simplicity)
- SSL Termination to be done on the AWS Load Balancer.
- The Loadbalancer will be of the classic type. An application Loadbalancer can also bee configured but requires more details for the same.
- On the servers, the UI component would be served by a webserver (apache/nginx)
- The security group for the servers will allow traffic only from the Loadbalancer and if required from the Bastion server.
- The security group for the AWS Loadbalancer will allow traffic only on ports 80 & 443.

### Application
- The Java application(s) will be talking to the RDS backend . 
- Read requests will be sent to the Read replicas in the RDS while writes will be sent to the Master DB 
- If required Redis can be used as a local key/value store to speed up performance.
- Backup for the application can be achieved by creating Life Cycle policies for the EBS volumes on the server with a rentention of period of 15 days and 2 snapshots per day.
- The application will run from a user account with limited privileges.

### Database
- The RDS instances will be run in a Multi-Az mode
- Read replicas will be enabled to reduce the burden on the main DB
- Snapshot backup will be enabled with retention of 14 days (minimum)
- The RDS security group will allow traffic only from the VPC CIDR block.
- The RDS instance will be accessed through a specific user account with limited privileges
