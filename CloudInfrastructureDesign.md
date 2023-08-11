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
    
  1. Client Organizational Virtual Private Cloud:
     * **(via AWS VPC)**: Globex elects to choose a cloud model for its secured infrastructure to meet its stated need to be highly elastic and rapidly scalable.

       * Create subnets - Subnetting is a security measure related to the "*Defense in Depth*" concept. The creation of subnets allows leveraging of firewalls (represented here by the element of "Security Groups") to manage authorized communication between the subnets.
     
         1. **"Public Subnets"** (1) - Our public subnet will function as a buffer zone or a **"DMZ"** to safeguard our assets on the Globex Internal Network. Here we will station our servers (Web, Domain, File, etc.) that will be able to access the Internet. Higher layers of security can be achieved by further subnetting this space (e.g. to create 'quarantine zones' during Incident Response, or 'sandboxes areas' in order to conduct testing prior to implementing a change.)

            * The Security Group of the Globex Cloud only allows for assets located in the "Public Subnet" to access the Internet. This is done to maintain

            * **Globex Internet Gateway (Globex IGW)**- This is the primary means of connecting the DMZ to the Internet outside of GCC. The Public Subnet uses the Globex IGW as a security boundary. While not the most secure form of connection, an IGW is still a point at which further measures (e.g. Intrusion Detection Systems or Intrusion Prevention Systems may be deployed to further secure the GCC.)
             
               * Network Address Translation (NAT) can also be configured on Globex IGWs to support security "*Defense in Depth*"

            * **Globex Virtual Private Network (VPN)-**  A more secure means of connecting to the DMZ is by **VPN**, which will connect here via ***Virtual Private Gateway (VGW)*** to enhance security by obscuring traversing the VPN while allowing transitioning assets access to our resources during migration to our Internal Networks. They can be added to Security Group (SG) allowlists to facilitate this. Globex VPNs (and partner VPNs) must use the IPSec protocol in order to be  (e.g. OpenSwan, StrongSwan, OpenVPN, pfSense, etc.). On AWS, these systems may work with a ***Customer Private Gateway (CGW)*** to assist in completing the VPN connection Globex's **VPG.**

              * **Captive Portal-** The VPN tunnel will have a **Captive Portal** enabled on it to increase Globex's security posture via *Defense in Depth*. Use of this Captive Portal will ensure that access is only achieved by users with written authorization (from Globex) and credentials granted by the Domain Controller. Use of **Captive Portal** in conjunction with Active Directory will identify and authenticate the user seeking access. 

              * Contractors may also apply for access to connect to our VPN in this DMZ area. Only with special written authorization will contractors access the Globex Internal Network Infrastrucutre (on a case-by-case basis only).  

            * **Security Groups (SGs)** of the Globex Cloud only allow for assets located in the "Public Subnet" to access the Internet. In this regard, they function primarly as firewalls- enforcing rules set to allow or deny certain types of traffic. 

              * **Public Security Group** - Configured on the Public Subnet 

              * **Private Security Group** - Configured on the Private Subnet

         2. "Private Subnets" (1) - Our private subnets will comprise the Globex Internal Network Infrastrucutre (GINI). This network is where the heart of Globex will operate.

            *  These private subnets can be further broken down to the Business Unit (BU) Level

               * Within Business Units, Organizational Units (OUs) may be further segmented into ***Virtual Local Area Networks (VLANs).***

      * **Endpoints in Globex's Public Subnets**
         * As stated before, endpoints in the Public Subnet area are likely to be Functional Servers (e.g. web servers, file servers, redundant servers, etc.), but the endpoints here could also be proxies, or remote Globex users connecting through the **Virtural Private Gateway**. Resources here can be further subnetted to support sandboxes, quarantine zones for incident response, and even guest access to Internet (in waiting rooms, lobbies, etc.)

      * **Endpoints in Globex's Private Subnets**
        * Most of Globex's on-premise workstations will be located here. Further subnetting may host closed-circuit resources, executive-level access, Company Intranet, and more.

  2. Existing Logical Network Element Details (Globex)

| Network Element Name | Description | IP Address (CIDR) | Operating System (OS) | ID |
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
        * As Globex continues to expand and partner with new companies to provide more capabilities, assessment of acquired Information Technology assets may prove to be inadequate for incorporation to either the GCC or GINI. In these cases, allowances must be made to bring the existing facilities up to a minimum standard while a transition plan is approved and implemented. 
        * Proper cataloguing of this equipment is essential to provide consistent support during the transition period.

  2. Globex Cloud
       * Ideally all new acquisitions with be compatible with the GCC Infrastrucutre outlined in this Network Architecture Design Statement.


## Evaluation Result & Discussion

  1. Yearly Assessments of this Network Architecture Design Statement will be conducted to keep pace with emerging trends, threats and opportunies to reduce Globex's attack surface.

## Version Control:
* 08/11/2023 - Ben Hobbs
