# Challenge 6

## Challenge Statement
 CloudFoxable  First Flag

## Solution
- **Created a Free AWS Account**.
  
- The system included the addition of a new non-root administrative operating account. Using non-root authentication is standard practice for security protection.

- An access key appeared for the newly made user. The CLI required this key to establish programmatic access to all AWS resources.

- I installed AWS CLI and Terraform software followed by system path configuration of their binaries. Deployment and configuration of infrastructure used Terraform as an automated tool.

- Verify the Installation using below commands
```bash
aws help
terraform help
```
It gave the options and the parameters that can be passed with each command

- **Configure AWS CLI** with the account that we created in first step
```bash
aws configure
```
It asked for the region, Access Key, Client ID. 

- To check if the use was set correctly, executed the below command
```bash
aws sts get-caller-identity
```
then ,it give an output as 
```json
{
    "UserId": "<UID>",
    "Account": "<AID>",
    "Arn": "<ARN>"
}
```
confirm that credential were correctly configure and allow to proceed

- Cloned the CloudFoxable repository
```
git clone https://github.com/BishopFox/cloudfoxable
```
This download the CloudFoxable repository into local directory.

-  Now navigated to the AWS directory and Copy the example Terraform variables file
```bash
cp terraform.tfvars.example terraform.tfvars
```

- Now Initialized Terraform
```
terraform init
```
Downloaded Terraform providers and initialized the working directory.

- Edited the variable file to add my local AWS PROFILE
```
aws_local_profile = "<MY_LOCAL_PROFILE_NAME>"
```

- Now i run terraform plan to see the resources that were going to be created, then terrafom apply create the Resources in AWS, resources and displayed key information, including credentials for ctf-starting-user.
```
terraform plan
terraform apply
CTF_Start_User_Access_Key_Id = "**************"
CTF_Start_User_Secret_Access_Key = <sensitive>
```
- Now, created a profile as given in the output using this command given below
```
echo "" >> ~/.aws/credentials && echo "[cloudfoxable]" >> ~/.aws/credentials && echo "aws_access_key_id = `terraform output -raw CTF_Start_User_Access_Key_Id`" >> ~/.aws/credentials && echo "aws_secret_access_key = `terraform output -raw CTF_Start_User_Secret_Access_Key`" >> ~/.aws/credentials && echo "region = us-west-2" >> ~/.aws/credentials
```

- And Env variables are to be set using below mention command
```
AWS_ACCESS_KEY_ID=`terraform output CTF_Start_User_Access_Key_Id`
AWS_SECRET_ACCESS_KEY=`terraform output CTF_Start_User_Secret_Access_Key`
AWS_REGION=us-west-2
```

- Then verify the credentials
```
aws sts get-caller-identity --profile cloudfoxable
```
- Below was the flag value in the output

I thoroughly examined the outcome produced by the terraform apply command following the setup process. The vital command showed how to establish my AWS CLI using the ctf-starting-user. The first flag retrieval depended on this particular user.
Every flag follows this specific syntax format: FLAG{challengeName::CamelCaseText}.
```
FLAG{congrats_you_are_now_a_terraform_expert_happy_hunting}
```
Copy the flag and pasted it. And it gave `Success` with green box color.



## Reflection
# What was your approach?
I followed a systematic protocol to establish AWS CLI, Terraform and security tools correctly before moving ahead. I analyzed Terraform outcome while using enumeration tools to search for potential valuable data.

# What was the biggest challenge?
Installation and configuration of essential tools needed special attention regarding IAM permissions management systems and profile switching procedures.

# How did you overcome the challenges?
Each stage of the challenge required my systematic completion while watching the terraform apply output. I achieved success by strictly implementing all instructions to set up the correct profile and user combination in my AWS CLI configuration. During debugging I utilized aws sts get-caller-identity to inspect my setup while I studied Terraform outputs in depth as well as using AWS enumeration tools to detect configuration errors.

# What led to the break through?
The analysis of Terraform output resulted in discovering an accessible S3 bucket that became the critical finding. The AWS CLI tools helped me list contents that revealed I could retrieve the flag. The breakthrough occurred when I setup proper configuration of my AWS CLI which let me reach Terraform-deployed resources. After establishing correct setup I retrieved the first flag from the output.

# On the blue side, how can the learning be used to properly defend the important assets? 
My experience emphasized the necessity of least privilege access administration for IAM users along with S3 bucket public access auditing and limiting and continued Terraform configuration assessment for misconfiguration prevention. Cloud environment monitoring systems need to track and detect unauthorized access and exposed sensitive data. Setting up cloud deployments correctly together with secure access management represents the most essential lesson learned about cloud resource implementation. The protection of sensitive cloud assets would demand from me that I establish proper IAM roles and policies which restrict unauthorized access. An active monitoring of Terraform outputs together with AWS CLI configurations enables the identification and mitigation of potential sensitive data exposure or flags.
