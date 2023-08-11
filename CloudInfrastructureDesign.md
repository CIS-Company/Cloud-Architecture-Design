# Cloud Architecture Design Statement

## Executive Summary

  * SilverLine Security is dedicated to providing our clients the most reliable and secure solutions to ensure confidence in both business operation and compliance audits. To this end, we have designed a custom cloud architecture concept to preserve the productivity and security of our client against the growing global threat of cybercrime (consisting of hactivists and Advanced Persistent Threats (APTs)) seeking to profit by disrupting business operations.

## Needs and Impact

## Client Stated Priorities & Needs

Our client has articulated several needs to prioritize when designing their cloud architecture:

* Compliance - Maintain continuous demonstrable compliance with the National Institute of Standards and Technology's Special Publication 800-53 (NIST 800-53)
* Logging - Deliver a secure and reliable means of accessing their systems. Using industry best practices, design a access control model that protects the organization against privilege creep, minimizes the effect that a single insider threat may have on the infrastructure, and implement processes that suitably manage sensitive permission sets to provide least privilege. 

 * Monitoring - Our chosen solution must also ensure all elements of our company are connected to each other to facilitate monitoring.
       
 * Threat Detection and Response - Security is imperative to the reliability of our client organization and providing consistent measures to create a comprehensive defense in depth are critical to safeguarding customer data.

## Methodology
### Analyze Requirements
     
  * Transitioning Partner former Infrastructure

     While the client does have data to transition, there is no current cloud infrastructure to transfer from. SilverLine will be designing and implementing a complete custom solution 

 ### Logical CLoud Architecture Design
    
  1. Client Organizational Virtual Private Cloud **(via AWS VPC)**: SilverLine elects to employ Amazon Web Services' Virtual Private CLoud offering because of a market-leading Identity Access Management capability and robust monitoring options.

      * Identity Access Management (IAM)

         * In order to reduce privilege creep, SilverLine Security will implement best practices identified with IAM to leverage Role-Based Access Control (RBAC) that is designed to place a group of customizable permissions together, and then confer that "Role" onto a User temporarily. When the User's responsibilities expire, their Role can be removed.
             
      * Server Hardening and Data Protection

         * Create subnets - Subnetting is a security measure related to the "*Defense in Depth*" concept. The creation of subnets allows leveraging of firewalls (represented here by the element of "Security Groups") to manage authorized communication between the subnets.
     
           1. **" SLSec Public Subnet"** (Qty 1) - Our public subnet will function as a perimeter zone or a **"DMZ"** to safeguard our consumer data assets on the Private Subnet. Here we will station our reverse proxy servers to recieve traffic and requests from the Internet. Higher layers of security can be achieved by further subnetting this space (e.g. to create 'quarantine zones' during Incident Response, or 'sandbox areas' in order to conduct testing prior to implementing a change.)

              * SLSec NAT Gateway (Qty 1) - NAT Translation happens as traffic leaves the Public Subnet through this gateway prior to being sent out through the SLSec Internet Gateway to communicate via the Internet.
              
              * **Security Groups (SGs)** allow for assets located in the "Public Subnet" to access the Internet. In this regard, they function primarly as firewalls (at the Instance-level) enforcing rules set to allow or deny certain types of traffic. Here we have the **RevProx-SG** - configured on the Public Subnet for the reverse proxies.

              * Reverse Proxies (Qty 4) 

            2. **Internet Gateway (SLSec IGW)** (Qty 1) - This is the primary means of connecting the DMZ to the Internet. The Public Subnet uses the Globex IGW as a security boundary. While not the most secure form of connection, an IGW is still a point at which further measures (e.g. Intrusion Detection Systems or Intrusion Prevention Systems may be deployed for further security.)
             
               * **Virtual Private Network (VPN)-**  A more secure means of connecting to the DMZ is by **VPN**, which will connect here via ***Transit Gateway (TGW)*** to enhance security by obscuring traversing the VPN to encrypt data "in transit" keeping consumer data safeguarded. 

         2. "Private Subnet" (Qty 1) - Our private subnet will host our clients assets that maintain their consumer and company data.  

             * **Hardened **
         * As stated before, endpoints in the Public Subnet area are likely to be Functional Servers (e.g. web servers, file servers, redundant servers, etc.), but the endpoints here could also be proxies, or remote Globex users connecting through the **Virtural Private Gateway**. Resources here can be further subnetted to support sandboxes, quarantine zones for incident response, and even guest access to Internet (in waiting rooms, lobbies, etc.)

      

  2. Existing Logical Network Element Details (Globex)

| Cloud Architecture Element Name | Description | IP Address (CIDR) | Operating System (OS) | ID |
|:----------------:|:---------------:|:---------------:|:---------------:|:----:|
| SLSec VPC | SilverLine Security VPC | 10.0.0.0/16 | AWS VPC | vpc-03c429b1e9fefca02
| SLSec Public subnet | SilverLine Security DMZ1 |10.0.1.0/24 | AWS VPC | subnet-0858c6b0d8519d865  
| SLSec Private subnet | SilverLine Security Internal | 10.0.3.0/24 | AWS VPC | subnet-032a266835b02d8b8
| SLSec IGW | SilverLine Security Internet Connection | IGW IP address | AWS IGW | igw-011a8bf616b568d43
| SLSec NAT GW | SilverLine Security NAT Gateway | VGW IP address | AWS VPG | vgw-070362c6caaaf9a5d
| SLSec UbuntuSrv (Hardened) | Ubuntu Server (PII-PCI) | 10.0.1.137 / 3.88.248.166 [Public IP] | AWS EC2 (AMI  Linux) | i-07b5a5c9c950cb2a9 | 
| SLSec Ubuntu RevProx1 | Ubuntu Reverse Proxy Server1 | 10.0.221.100 | AWS EC2 (AMI Linux) |
| SLSec Ubuntu RevProx2 | Ubuntu Reverse Proxy Server2 | 10.0.221.100 | AWS EC2 (AMI Linux) |
| SLSec Win19SrvDC | Windows 19 Server Domain Controller | Active Server Directory | Server IP address | Windows 19 Server 

### AWS Security Group1 (Private Subnet to Public Subnet)

 
| Rule | Source | Destination | Port | Protocol |Notes |
|:--------------:|:----------:|:------------:|:-----------:|:-----:|:-------:|
| Allow | 0.0.0.0/0 | 0.0.0.0/0 | 22 | TCP | SSH
| Allow | 0.0.0.0/0 | 0.0.0.0/0 | - | ICMP | ICMP

  
  3. Existing Logical Network Element Details (GreenGenius)

| Network Element Name | Description | IP Address (CIDR) | Operating System (OS) | ID |
|:----------------:|:---------------:|:---------------:|:---------------:|:----:|
| GreenGenius VPC | GreenGenius VPC | 172.31.0.0/16 | AWS VPC | vpc-0f78af57f6ecb08e6
| GreenGenius EC2-3 | GreenGenius Endpoint | 172.31.10.122 / 44.201.2.52 [Public IP] | AWS EC2 (AMI Linux) | 1-0c7c98f557a995e27
| GreenGenius CGW | GreenGenius Customer Gateway | BGP ASN 65000 / 44.201.2.52 [Public IP] | AWS CGW | cgw-00b24a574063b5f6d

  
## Physical Network Design
    
  1. On-Premise resources
        * For this architecture design concept, all cloud resources will be IaaS. There are no private on-premises resources to manage to maintain the VPC.


## Evaluation Result & Discussion

  1. Yearly Assessments of this Cloud Infrastructure Design Statement will be conducted to keep pace with emerging trends, threats and opportunities to reduce client attack surface and safeguard consumer data.

## Version Control:
* 08/11/2023 - Ben Hobbs
