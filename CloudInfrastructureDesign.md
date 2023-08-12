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

 ### Logical Cloud Architecture Design
    
  1. Client Organizational Virtual Private Cloud **(via AWS VPC)**: SilverLine elects to employ Amazon Web Services' Virtual Private CLoud offering because of a market-leading Identity Access Management capability and robust monitoring options.

      * Identity Access Management (IAM)

         * In order to reduce privilege creep, SilverLine Security will implement best practices identified with IAM to leverage Role-Based Access Control (RBAC) that is designed to place a group of customizable permissions together, and then confer that "Role" onto a User temporarily. When the User's responsibilities expire, their Role can be removed.
             
      * Server Hardening and Data Protection

         * Create subnets - Subnetting is a security measure related to the "*Defense in Depth*" concept. The creation of subnets allows leveraging of firewalls (represented here by the element of "Security Groups") to manage authorized communication between the subnets.
     
           1. **" SLSec Public Subnet"** (Qty 1) - Our public subnet will function as a perimeter zone or a **"DMZ"** to safeguard our consumer data assets on the Private Subnet. Here we will station our reverse proxy servers to recieve traffic and requests from the Internet. Higher layers of security can be achieved by further subnetting this space (e.g. to create 'quarantine zones' during Incident Response, or 'sandbox areas' in order to conduct testing prior to implementing a change.)

              * **SLSec NAT Gateway** (Qty 1) - NAT Translation happens as traffic leaves the Public Subnet through this gateway prior to being sent out through the SLSec Internet Gateway to communicate via the Internet.
              
              * **Security Groups (SGs)** allow for assets located in the "Public Subnet" to access the Internet. In this regard, they function primarly as firewalls (at the Instance-level) enforcing rules set to allow or deny certain types of traffic. Here we have the **RevProx-SG** - configured on the Public Subnet for the reverse proxies.

              * **Reverse Proxies** (Qty 4) in the Public Subnet allow requests from the Internet to be fielded by these servers instead of communicating directly with the sensitive data on the protected servers in the private subnet. In this manner, the proxies form yet another layer of protection. The implementation of two of them for each server in the Private Subnet provides for increased availability and performance (load balancing)

            2. **Internet Gateway (SLSec IGW)** (Qty 1) - This is the primary means of connecting the DMZ to the Internet. The Public Subnet uses the Globex IGW as a security boundary. While not the most secure form of connection, an IGW is still a point at which further measures (e.g. Intrusion Detection Systems or Intrusion Prevention Systems may be deployed for further security.)
             
               * **Virtual Private Network (VPN)-**  A more secure means of connecting to the DMZ is by **VPN**, which will connect here via the ***Transit Gateway (TGW)*** to enhance security by obscuring traffic through the VPN. Traffic will also be encrypted so that data "in transit" keeps consumer data safeguarded. 

           3. **SLSec Private Subnet** (Qty 1) - Our private subnet will host our clients assets that maintain their consumer and company data.  

             * **Center for Internet Security (CIS) Hardened Servers (Ubuntu and Windows)**
                Endpoints in placed in the Private Subnet area are likely to be an organization's most valuable assets. Resources here are the focus of protection as we greatly value both our client's internal business practices and intellectual property and the trust they have been allocated to safeguard their consumer data.

  2. Existing Cloud Architectural Details 

| Cloud Architecture Element Name | Description | Public IPv4 Address | Private IPv4 Address | Operating System (OS) | ID |
|:----------------:|:---------------:|:---------------:|:---------------:|:----:|:----------|
| SLSec VPC | SilverLine Security VPC | 172.31.0.0/16 | N/A |AWS VPC | vpc-064db35c71b2b7c9e | 
| SLSec Public subnet | SilverLine Security DMZ1 | 172.31.0.0/20 | N/A |AWS VPC | subnet-06ef193843ec12809  
| SLSec Private subnet | SilverLine Security Private | 172.31.48.0/20 | N/A |AWS VPC | subnet-05e26d69270db4ef5
| SLSec IGW | SilverLine Security Internet Connection | N/A | N/A  |AWS IGW | igw-0bfc1c3d8e5e09c4d
| SLSec NAT GW | SilverLine Security NAT Gateway | 35.83.136.231 | 172.31.12.32 |AWS NAT | nat-023c11e00248a3578
| SLSec UbuntuSrv (Hardened) | Ubuntu Server (PII-PCI) | 34.222.184.96 | 172.31.54.119 | AWS EC2 | i-024ce2e221b5aeea8
| SLSec Ubuntu RevProx 1 | Ubuntu Reverse Proxy Server 1 | 52.88.45.109 | 172.31.2.151 | AWS EC2 | i-024bdf23631a40305
| SLSec Ubuntu RevProx 2 | Ubuntu Reverse Proxy Server 2 | 18.237.129.183 | 172.31.12.226 | AWS EC2 | i-066eeba0e5f6afe01
| SLSec Win19SrvDC | Windows 19 Server Domain Controller | 34.210.191.221 | 172.31.26.31 | AWS EC2 | i-025790da0391f60dc
| SLSec Win19RevProx 1 | Windows Reverse Proxy Server 1 | 54.149.175.161 | 172.31.5.63 | AWS EC2 | i-0d62c7c37e861b019
| SLSec Win19RevProx 2 | Windows Reverse Proxy Server 2 | 18.237.148.68 | 172.31.14.0 |AWS EC2 | i-037138e927f3ee3ec
| SLSec Open VPN | VPC VPN connection | 34.222.184.96 | 172.31.54.119 | AWS EC2 | i-08acb5b9936d5df13
 

## Physical Network Design
    
  1. On-Premise resources
        * For this architecture design concept, all cloud resources will be IaaS. There are no private on-premises resources to manage to maintain the VPC.


## Evaluation Result & Discussion

  1. Yearly Assessments of this Cloud Infrastructure Design Statement will be conducted to keep pace with emerging trends, threats and opportunities to reduce client attack surface and safeguard consumer data.

## Notional Elements of Note (Not to be included with the Cloud Architectural Design)

| Element Name | Description | Public IPv4 Address | Private IPv4 Address | Operating System (OS) | ID |
|:----------------:|:---------------:|:---------------:|:---------------:|:----:|:----------------|
| Kali Linux OS | SilverLine Red Team Instance | 52.89.109.34 | 172.31.46.47 | Kali Linux | i-0aba6bf21f371897c |
| Strat RedTeam Attack | SilverLine Red Team Subnet | 172.31.32.0/20 | N/A | N/A | subnet-09baef25065a7fac0 |
## Version Control:
* 08/11/2023 - Ben Hobbs
