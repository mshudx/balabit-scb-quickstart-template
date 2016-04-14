# About
This repository contains Azure Resource Manager template that will deploy Balabit Shell Control Box virtual machine within a test environment:
* Azure Virtual Network with two subnets:
  * subnet-ad
  * subnet-scb
* Windows Server 2012 R2 Datacenter configured as Domain Controller
* The vnet is configured to use the domain controller
* all the neccessary things like storage account, network security groups, private and public ip addresses

# Deployment
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmshudx%2Fbalabit-scb-quickstart-template%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fmshudx%2Fbalabit-scb-quickstart-template%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://armviz.io/visualizebutton.png"/></a>
 
To deploy use the links above and follow the deployment wizard, alternatively you can use one of the command line tools like azure-cli: 

```
azure group create --name "scb-test" --location "West Europe"
azure group deployment create --resource-group scb-test --name "scb-test-deployment" --template-file .\azuredeploy.json --parameters-file .\azuredeploy.parameters.json --mode Incremental
```

# What is Balabit Shell Control Box?
[Shell Control Box (SCB)](https://azure.microsoft.com/en-us/marketplace/partners/balabit/balabit-shell-control-box/) is a turnkey activity monitoring appliance that controls access to remote servers, virtual desktops, or networking devices, and records the activities of the users accessing these systems. For example, it records as the system administrators configure your database servers, or your third-party provider manages your web-server. The recorded audit trails can be replayed like a movie to review the events exactly as they occurred. The content of the audit trails is indexed to make searching for events and automatic reporting possible. SCB is especially suited to supervise privileged-user access as mandated by many compliance requirements, like the PCI-DSS or ISO 2700x. SCB records all administrative traffic (including configuration changes, executed commands, etc.) into audit trails. All data is stored in encrypted, timestamped and signed files, preventing any manipulation. In case of any problems (server misconfiguration, unexpected shutdown, etc.) the circumstances of the event are readily available in the audit trails, thus the cause of the incident can be easily identified. SCB enables enterprises to audit the activity of privileged users across their on-premise and Azure cloud by showing "who is doing what" to prevent insider threats and data breaches.

# Configuration prerequisites
- [x] A valid license from Balabit (mailto:sales@balabit.com)
- [x] Optional: For alerting, the address of your SMTP server (Enter 0.0.0.0 to turn alerting off)
 
# Template parameters
_coming soon_

# Getting started
1. after successful deployment open https://`[dnsPrefix]`.`[location]`.cloudapp.azure.com/ in your browser (ignore any certificate issues for now)
  * for example https://scbtest.westeurope.cloudapp.azure.com/
2. click next to skip the `import old configuration` step 
3. accept the eula and upload your license
4. on the network tab fill out the required fields
  * physical interface: the static IP address you have configured in `[scbNicIPAddress]` parameter with prefix /24, for example: 10.0.0.4/24 
  * default gateway: typically you have to change the last number to .1 (xxx.xxx.xxx.1), for example: 10.0.0.1
  * hostname: the configured name of your vm, provided in `[scbVMName]` parameter, the default value: vm-scb
  * domainname: the FQDN of the AD Domain created, given in the required `[domainName]` parameter
  * DNS server: the private static IP address of the domain controller VM `[adNicIPAddress]`, alternatively you can use any public DNS server, the default is: 10.0.1.4
  * SMTP server: use your own SMTP server, or 0.0.0.0 to turn this feature off
  * Administrator's email: use your own email address
  * Timezone: select your timezone
5. Type in the new password for the admin and root users
6. Upload your cerificate, or generate a new one:
  * required fields are: country, organization name, organizational unit name
7. Accept the settings by clicking on the _Finish_ button 

> You'll be redirected to the internal administration page which points to the private IP address used by the VM. * To open the website outside of the vnet, replace the IP address with the domain name used to configure the box.

8. Log in with the admin user to begin RDP protocol forwarding configuration  
9. Under Policies -> Signing CAs, create a new test CA
10. Under RDP Control -> Domain membership, join the created domain
11. Under RDP Control -> Policies -> default policy: enable Network Level Authentication
12. Under RDP Control -> Connections create a new connection to forward RDP port to the domain controller's private fix ip (eg.: 10.0.1.4).
13. Estabilish the remote desktop connection