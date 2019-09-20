# AWS Cloud Formation
End state of the Infrastructure. Supported formats are Json or YAML.
#### Stacks
Cloud formation Template consists of set of AWS Resources (EC2, S3, ELB, VPC, DynamoDB etc..), once created it is known as a Stack (set of related resources as a single unit). For updating the resources within a template, you need to update a stack.
#### Change Sets
Summary of proposed changes before updating a stack 
#### Cloud Formation Template
```yaml
AWSTemplateFormatVersion:
Description:
Metadata:
Parameters:
Mappings:
Conditions:
Transform:
Resources:
Outputs:
```
## Beginners [1-5]
## I. Exercise Section 
### 1. Demonstrate sample Cloud Formation Template
		a. Creating EC2 Instance using CF Template
		b. Updating existing stack using CF Template
### 2. Intrinsic Funtions
       Syntax: !Join [*delimiter*,[*string1*,*string2*,*string3*,*...*]]	
### 3. Multiple Resources
	   For Example, During EC2 Launch Instance, there are 2 main aws resources need to be created. However, without these 2 main resources EC2 instance creation will not be successful or you cannot proceed further for EC2 instance creation. The 2 main resources are VPC and Security Groups.
	   a. Using !Ref Function, Lets see how we can use Cloud Formation Template to demonstrate this usage. 
	   b. Pseudo Parameters, Predefined Parameters available for Cloud Formation Template. Similar to Environmental Variables - You can find this demonstration in the location  templates/pseudo-multi.yaml
	   c. Mappings, - templates/mappings.yaml
	   d. Input Parameters,  templates/inputparameters.yaml
       e. Outputs, Provides access to information about resources within a stack - For example - Instance IP Address, DNS Name etc. 
            templates/outputs.yaml
### 4. Sample Web Application
        In this exercise, we will also demonstrate UserData and Helper Scripts.
		a. UserData - It will help to initialize starter scripts / application dependencies before EC2 instance is provisioned
		b. Helper Scripts
			***cfn-init*** - Read and Interprets MetaData to execute AWS::CloudFormation::Init
						cfn-init is used in MetaData section to define single or multiple configuration keys. 
						For Single config key, we need to use ***config:**** 
						For Multiple config keys, we need to use configSets. Configsets contain a list of config keys which is executed in the order we define.
			***cfn-signal*** - Helps to signal when resource or application is ready
			***cfn-get-metadata*** - Retrieve metadata based on a specific key
			***cfn-hup*** - This is used to check for updates to metadata and execute custom hooks
			
			Demo application available in the location - templates/sample-webapplication.yaml
### 5. Changesets Usage
       Process of recreating the resources with existing templates. For example, you can change Instance Name in the EC2 instance without underlying resources defined the cloud formation template.
       Following are the operations supported by ChangeSets:
       a. Create
       b. View
       c. Execute
       d. Delete
       Resource Changes may include - Logical Resource Id / Physical Resource Id, type of cloud formation resource(AWS::EC2::Instance), replacement of existing resources (creating new one and deleting old resource), and scope of the resource which has triggered the update. 
	   Based on the nature of the change, Action will be determined as ADD | MODIFY | REMOVE
	   
	   Following Scenarios will be demonstrated:
	   1. Updating SG group i.e,. removing 22 port from SG - Resulting in updating the SG. Group Description content had port 22 description. In the next step, we will update SG description with Port 80 description because we have removed port 22 from SG inbound rules.
	   2. Updating SG Group Description, which will lead to creation of new SG and deleting old SG. This behavior is because we haven't defined any VPC/Subnet. 
	   3. However, in step. 2, after defining vpc/subnet will also lead to creation of new SG and deleting old SG. This is expected. Once VPC / Subnet is defined, subsequent changes will not cause the creation of new SG and deleting old SG (Replacement).
	   4. Updating SG Description, which will update existing SG descriptions. As we have already defined VPC in step 3 which is expected behavior.
## Intermediate
## II. Advance Exercise Section			
### 6. Creating Multiple resources - EC2 Instance, RDS DB Instance(MySQL), and S3 Bucket
       Dynamically create EC2 Instance and DB Instance based on the region selected i.e., there is a usage Mapping Section in the template in which regions (only Asian region are configured for this template), ami's and instance types are defined.
	   This will demonstrate the usage of template portability and reusability. 
	   Template location - advance-templates/wp-infrastructure.yaml
	   
### 7. Installing and Configuring -  Web Server, PHP, WordPress using UserData Section 
       You may review User Data section to customize the steps or to correctly point out the php / webserver versions].
	   Verifying installations, once Cloud Formation Stack is created. [Make sure SSH port 22 and http 80 is opened in security group once EC2 is provisioned]
	   SSH to EC2 Instance, Follow below steps -
	  $ cd /var/www/html
	  
	  //Check WordPress Installation files
	  $ ls
	  
	  // Verify DB Connection Details by opening the file wp-config.php. These DB connection details was defined in User Data section.
	  $ vi wp-config.php 		  
	  
	  // Verify Httpd Service is running
	  $ ps -aux | grep httpd
	  $ cd /var/log/
	  $ ls
	  
	  // Go to End of the file. Verify Last Line which has updated date and time of the installation.
	  $ vi cloud-init-output.log 
		  
	   From Public DNS, You can see Word Press Initial Website where you need to enter WordPress Title, initial credentials and email. Later this page is redirected to Login Page to enter the credentials. After successful login, click on title where you see landing page of Word Press.
	   
	   Template location - advance-templates/wp-infrastructure-install.yaml
	   
### 8. Installing and Configuring -  Web Server, PHP, WordPress using Using cfn-init and cfn-hup  
	   <p align=”justify”>This is an effective and platform independent approach i.e., usually for Linux Platform, UserData Section consists of shell scripts whereas for Windows platform, UserData section use to be batch / powershell script. Therefore, for any Platform their is necessity of creating script supported by new platforms which makes process ineffective. Hence, resulting in many scripts which will difficult to manage.
	   
	   cfn-init and cfn-hup provides a effective way of implementing generic script supported by most of the platforms.

	   SSH to EC2 Instance to verify the logs.</p>
	   
	   $ cd /var/log/
	   
	   $ ls
	   
       cloud-init.log and cloud-init-output.log are generated when UserData Section is used
       cloud-init-output.log - output data of script executed from commands defined in UserData Section 	   
	   cfn-init.log and cfn-init-cmd.log are the files generated from cfn-init process
	   cfn-init.log - will provide command output of cfn-init process which is executed from metadata section
	   cfn-hup.log - generated from cfn-hup process when it is configured and started
	   
	   If we poll the logs from cfn-hup.log, 
	   
	   $ cat cfn-hup.log
	   
	   From the template, as it is polled every 5 minutes.
	   
	   Browse, Public IP Address - http://<publicip>/index2.html -> You can see string on the browser which is defined in Parameter Section of the template - "HelloWorld"
	   
	   Whenever this sample string is changed, cfn-hup detect the changes in the file index2.html. cfn-hup polls the changes. 
	   
       $ tail -f cfn-hup.log
	   
	   Template location - advance-templates/wp-infrastructure-cfn-init.yaml       
	   
### 9. Creation Policy and cfn-signal
       cfn-signal - will help to send signal to Creation Policy about resource creation completion signal to CreationPolicy.
	   
	   Template Location -
	   a. with no cfn-signal defined - advance-templates/wp-infrastructure-creationpolicy-no-cfn-signal.yaml
	   b. with cfn-signal defined - advance-templates/wp-infrastructure-creationpolicy-cfn-signal.yaml
	   
### 10. Deletion Policy
       Supported types are Delete, Retain (Supported by few AWS Resource types) and Snapshot (Supported by few AWS Resource types).
	   By Default, Its ***Delete*** Policy. 
	   To define, explicity you implement as ***DeletionPolicy: Delete***
	   
	   a. Delete Behavior
	   Template Location: advance-templates/wp-infrastructure-delete-policy-explicit.yaml
	   b. Retain / Snapshot
		Retain Operation - Will retain s3 Bucket and skips deleting even though the stack is deleted.
		Snapshot - First creates snapshot of RDS DB Instance then initiates deleting process.
	   Template Location: advance-templates/wp-infrastructure-db-snapshot_s3-retain-policy-explicit.yaml
      	   
	   