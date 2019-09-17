# AWS Cloud Formation
End state of the Infrastructure. Supported formats are Json or YAML.
#### Stacks
Cloud formation Template consists of set of AWS Resources (EC2, S3, ELB, VPC, DynamoDB etc..), once created it is known as a Stack (set of related resources as a single unit). For updating the resources within a template, you need to update a stack.
#### Change Sets
Summary of proposed changes before updating a stack 
#### CF Template
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

## Exercise Section 
### 1. Demonstrate sample Cloud Formation Template
		a. Creating EC2 Instance using CF Template
		b. Updating existing stack using CF Template
	
