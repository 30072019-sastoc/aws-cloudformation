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
## Exercise Section 
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
			
			
			
			
	   
	   
	   