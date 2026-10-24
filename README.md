# AWS-PROJECT
 EWB Cloud Labs --- AWS VPC Peering Project
A hands-on AWS networking project that demonstrates how to connect two
Amazon Virtual Private Clouds (VPCs) using VPC Peering, configure
subnet routing and security groups, and validate private connectivity
between EC2 instances.

AWS Region: Asia Pacific (Sydney) --- ap-southeast-2
Services: Amazon VPC, VPC Peering, Amazon EC2, Internet Gateway,
route tables, subnets, security groups
Project focus: Network design, private connectivity, SSH access,
connectivity testing, troubleshooting, evidence documentation, and
resource cleanup
1. Project Overview
This project creates two separate VPCs with non-overlapping private IPv4
CIDR blocks and connects them through a VPC Peering connection.
- VPC A contains a public subnet for the Bastion Host and a
  private subnet for App-A.
- VPC B contains a private subnet for App-B.
- Route tables direct traffic destined for the other VPC through the
  peering connection.
- Security groups control the traffic allowed to reach the EC2
  instances.
- Connectivity is tested and recorded using terminal output and
  screenshots.
The project evidence is arranged in a numbered sequence that follows the
implementation workflow, from VPC creation to testing and cleanup.
2. Architecture and Address Plan
  Resource          Name              CIDR / Private    Purpose
                                      IPv4              
  VPC A             ewb-vpc-a       10.10.0.0/16    Hosts Bastion and
                                                        App-A

  VPC A public      ewb-a-public    10.10.1.0/24    Subnet for
  subnet                                                Bastion access

  VPC A private     ewb-a-private   10.10.2.0/24    Private
  subnet                                                application
                                                        subnet

  Bastion EC2       ewb-bastion     10.10.1.10      Administrative
                                                        entry point

  App-A EC2         ewb-app-a       10.10.2.10      Private workload
                                                        in VPC A

  VPC B             ewb-vpc-b       10.20.0.0/16    Hosts App-B

  VPC B private     ewb-b-private   10.20.1.0/24    Private
  subnet                                                application
                                                        subnet

  App-B EC2         ewb-app-b       10.20.1.10      Private workload
                                                        in VPC B

  VPC Peering       ewb-a-b-peer    Connects VPC A    Private inter-VPC
  connection                          and VPC B         routing

  Internet Gateway  ewb-igw-a       Attached to VPC A Internet route
                                                        for the public
                                                    subnet
The VPC CIDRs 10.10.0.0/16 and 10.20.0.0/16 are non-overlapping
ranges within RFC 1918 private IPv4 address space. App-A and App-B are
intended to use private IPv4 addresses without public IPv4 addresses.
Configuration note: Resource names and addresses above reflect the
project details recorded during the lab. Check the screenshots and
your AWS configuration if a resource was renamed or assigned a
different address.

3. Routing and Traffic Flow
The intended routing design is:
- VPC A route tables contain a route for 10.20.0.0/16 targeting the
  VPC Peering connection.
- VPC B's route table contains a route for 10.10.0.0/16 targeting
  the VPC Peering connection.
- VPC A's public route table uses 0.0.0.0/0 targeting ewb-igw-a.
- The Bastion is the intended administrative entry point for reaching
  private instances.
- Security-group rules must allow the required traffic in addition to
  the route tables allowing the network path.
VPC Peering does not automatically create routes or permit traffic.
Route tables, security groups, operating-system firewalls, and SSH
credentials must all be configured appropriately.
4. Project Objectives
- Create two VPCs with non-overlapping private IPv4 CIDR blocks.
- Create public and private subnets.
- Configure an Internet Gateway for VPC A's public subnet.
- Configure route tables and subnet associations.
- Create security groups for the Bastion and application instances.
- Launch the Bastion, App-A, and App-B EC2 instances.
- Create and accept the VPC Peering connection.
- Configure routes for communication between the VPCs.
- Test SSH access and ICMP connectivity where permitted.
- Capture screenshots and document test outcomes.
- Clean up lab resources after evidence has been saved.
5. Repository Contents and Evidence Sequence
The following list follows the folder names provided for the project
materials. Some names contain the original spelling and spacing used in
the evidence folders; preserve those names or update this section if you
rename them in GitHub.
  Sequence                Folder / material name                 Evidence covered
  1                       1.vpc A and b                        Creation of VPC A and
                                                                 VPC B

  2                       2.VPC SUBNETS                        Subnet creation and
                                                                 CIDR configuration

  3                       3.Internet gateways                  Internet Gateway
                                                                 creation and attachment

  4                       4.Route tables                       Routes and subnet
                                                                 associations

  5                       5.Security groups                    Security-group
                                                                 configuration

  6                       6.EC2 instances (3)                  Three EC2 instances and
                                                                 their settings

  7                       7.Peering connection a to b          VPC Peering creation
                                                                 and connection status

  8                       8.peering proof of a                 Connectivity evidence
                                                                 associated with VPC A

  9                       9.peering proof of b                 Connectivity evidence
                                                                 associated with VPC B

  10                      10.ewc-screens hot test case proof   Test-case screenshots
                                                                 and terminal outputs

  11                      11. clean up evidence                Evidence of resource
                                                                 cleanup

  Report                  Annotated test evidence PDF / editable Explanations of
                          Word document                          screenshots and
                                                                 observed outputs

  Diagram                 AWS VPC Peering architecture diagram   Visual summary of VPCs,
                                                                 subnets, instances, and
                                                             peering
If your repository uses slightly different folder names, edit the table
to match the exact names shown in GitHub.
6. Testing and Validation
The evidence package documents the lab workflow and includes SSH and
ICMP test screenshots. The key validation points are:
1. Confirm the VPC Peering connection is active.
2. Confirm the route tables contain routes to the remote VPC CIDR with
   the peering connection as the target.
3. Confirm the EC2 instances use the expected private IP addresses.
4. Test ICMP connectivity between App-A (10.10.2.10) and App-B
   (10.20.1.10) when security groups and host firewalls permit ICMP.
5. Test the intended SSH path through the Bastion.
6. Record both successful and unsuccessful attempts accurately.
Interpreting the results
Some screenshots show successful ping replies between App-A and App-B,
including tests in both directions. Other screenshots show a ping
failure or an SSH authentication failure during troubleshooting.

An output such as
Permission denied (publickey,gssapi-keyex,gssapi-with-mic) means SSH
authentication did not complete. It can occur even when the remote SSH
service is reachable; it does not, by itself, mean the VPC Peering route
is broken.

A ping failure indicates that replies were not received at the time of
that test. It does not independently identify whether the cause was a
route, security-group rule, host firewall, or another setting. Refer to
the corresponding screenshots for each observed result and do not mark a
test as passed unless its evidence supports that conclusion.
7. Security Considerations
- Keep application instances in private subnets without public IPv4
  addresses.
- Limit SSH access to the required source security group or a trusted
  administrator address.
- Avoid allowing SSH (TCP/22) from 0.0.0.0/0.
- Keep ewb-lab.pem on the administrator's computer and set
  appropriate file permissions.
- Never upload private keys, passwords, AWS access keys, session
  tokens, or other secrets to GitHub.
- Use the minimum security-group rules needed for the lab.
- Verify route targets, subnet associations, and resource IDs before
  making changes.
- Remove temporary troubleshooting resources when they are no longer
  needed.
8. Cleanup
After saving the screenshots, architecture diagram, and reports:
1. Terminate the lab EC2 instances: ewb-bastion, ewb-app-a, and
   ewb-app-b.
2. Delete the EC2 Instance Connect Endpoint created for
   troubleshooting, if it is no longer required.
3. Delete the VPC Peering connection.
4. Detach and delete the Internet Gateway associated with VPC A.
5. Delete the lab VPCs and their dependent resources, such as subnets
   and custom route tables.
6. Review and remove lab-only security groups after dependent resources
   are removed.
7. Check for leftover Elastic IP addresses, EBS volumes, snapshots, NAT
   Gateways, and other billable resources.
8. Review AWS Billing and Cost Management for charges that may have
   accrued.
Confirm that each selected resource belongs to this lab before deleting
it. Resource deletion does not reverse charges already incurred.
9. Skills Demonstrated
- AWS VPC and subnet design
- Private IPv4 addressing and CIDR planning
- Internet Gateway and route-table configuration
- VPC Peering and cross-VPC routing
- EC2 instance deployment
- Security-group configuration
- SSH access and connectivity troubleshooting
- ICMP/ping validation
- Technical evidence capture and documentation
- AWS resource cleanup and cost awareness
10. Disclaimer
This repository documents an educational lab. The architecture, names,
addresses, and test descriptions are based on the project materials and
the AWS setup details recorded during implementation. Screenshots are
the evidence of individual test outcomes; they should not be interpreted
as a guarantee that the resources remain deployed or that the same
results will occur after configuration changes.

AWS resources can incur charges while they exist. Remove lab resources
when finished and review the AWS billing console.
Project: EWB Cloud Labs --- AWS VPC Peering
Region: ap-southeast-2 (Sydney)
Owner: ANUJ SAJANKAR
