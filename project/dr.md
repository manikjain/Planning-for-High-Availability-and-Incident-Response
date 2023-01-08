# Infrastructure

## AWS Zones
primary region: us-east-2 (a/b/c)
secondary region: us-west-1 (c/b)

## Servers and Clusters

### Table 1.1 Summary

| Asset                    | Purpose                                                      | Size      | Qty                                                          | DR                                                           |
| ------------------------ | ------------------------------------------------------------ | --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| EC2 (primary, secondary)            | Web server with flask app running (3 nodes in each region)       | t3.micro  | 6 (3x2)                                                            | Replicated to the secondary region (us-west-1)                        | |
| EKS Cluster    | Managed kubernetes cluster in AWS with 2 active nodes used for hosting monitoring services (prometheus and grafana)  | t3.medium | 1 cluster in each region with 2 nodes  | Cluster is created in both us-east-2 and us-west-1                      |
| VPC                      | Virtual Private Cloud (VPC) for creating other AWS services |           |     2                                                         | One each in primary and secondary region                         |
| Application Load Balancer            | For load balancing traffic to the EC2 web servers |           |       2                                                       | One each in primary and secondary region                         |
| RDS (AWS Relational Database System) | Managed SQL database in AWS with two instances (read, write) | t3.small  | 2 clusters (4 instances)          | Deployed in us-east-2 and replicated to us-west-1 |


### Descriptions

EC2 - Cloud VMs in AWS used for hosting flask web application. 3 instances are deployed in both us-east-2 and us-west-1 for high availability.
EKS (Elastic Kubernetes Cluster) - Managed kubernetes cluster used for hosting monitoring services (prometheus and grafana).
ALB (Application Load Balancer) - Deployed in each region and used for load balancing traffic to the EC2 web server instances.
VPC - Used for creation of a private cloud in AWS. One VPC is deployed in each region. A single VPC can only span to one region.
RDS (AWS Relational Database System) - Used as a database with 2 instances (one each for read and write) and replicated to the secondary region (us-west-1).

## DR Plan
### Pre-Steps:
- Setup terraform (if non-existent) for the secondary region (e.g. us-west-1) similar to us-east-2 while modifying the templates to add proper config as applicable to the secondary region.
- Use Terraform (IaC) to create infrastructure in the secondary region.
- Verify connectivity and ensure the secondary region is working as expected and similar to the primary region.

## Steps:
- Use a DNS service like Route53 to auto-failover to the secondary region load balancer based on a health check or using manual failover
  - A health check configured in Route53 which periodically checks the load balancer at some endpoint for a success code. If somehow the health check fails, configure Route53 to auto-switch traffic to the secondary ALB in us-west-1.
- For the database, utilise auto-failover in AWS or use manual failover in case of issues noticed with RDS in the primary region.
  - Use AWS actions to failover.
  - Or AWS native auto-failover for RDS.