## Solution - Detailed Steps
1. **Enable Bastion Host in Terraform** 
- Edit cloudfoxable/aws/terraform.tfvars

- Switch the challenge flag to true
```
bastion_enabled = true
```

2. **Run Command terraform apply**
   
   By run this command, it throw a error ` Error: "policy" contains an invalid JSON: invalid character '\n' in string literal `. This is because of the ` USER_IP ` variable.
   Then, I config `USER_I` manually and  after then agin run command `terraform apply` after that i got `Apply complete` message and `output`

3. **Install Cloudfox**
   
   For install cloudfox I webt to github link `https://github.com/BishopFox/cloudfox/releases` and download `cloudfox-linux-amd64.zip` after that i use unzip command and move cloudfox to  `/usr/local/bin`
   comand are given below
   ```
   unzip cloudfox-linux-amd64.zip
   sudo mv ~/cloudfox/cloudfox /usr/local/bin
   cloudfox --version
   ```
4. **Find Instance ID**
   
   By running command given below to list out the instances and find the instance ID:
   ```
   cloudfox aws -p cloudfoxable instances -v2
   ```
   After running this command the loot is cached
   
   ```
   [instances][cloudfoxable] Loot written to [/home/vikram/.cloudfox/cloudfox-output/aws/cloudfoxable-135808940242/loot/instances-ec2PrivateIPs.txt]

   [instances][cloudfoxable] Loot written to [/home/vikram/.cloudfox/cloudfox-output/aws/cloudfoxable-135808940242/loot/instances-ec2PublicIPs.txt]

   [instances][cloudfoxable] Loot written to [/home/vikram/.cloudfox/cloudfox-output/aws/cloudfoxable-135808940242/loot/instances-ssmCommands.txt]

   [instances][cloudfoxable] Loot written to [/home/vikram/.cloudfox/cloudfox-output/aws/cloudfoxable-135808940242/loot/instances-ec2InstanceConnectCommands.txt]
   ```

5. **Install Session Manager Plugin and Use the SSM Command**

   I follow the AWS documentation to install the Session Manager plugin by using below given commands
   ```
   curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
   sudo dpkg -i session-manager-plugin.deb
   ```
   To verify the Session Manager plugin is installed or not I use
   
   ```
   session-manager-plugin
   ```
   It give a this message `The Session Manager plugin is installed successfully. Use the AWS CLI to start a session.` which means plugin installed.
   
   To Connect instance with SSM
   
   ```
   aws ssm start-session --target <instance-id>
   
   ```
   my instance id = i-0d42eb03155132c37

6. **Check Permissions**  
   by run this command to check the instance’s permissions:
   ```
   cloudfox aws -p cloudfoxable permissions --principal [NameOfInstanceRole]
   ```
   NameOfInstanceRole = bastion
   
 
7. **Access the S3 Bucket**
   by the above step identify instance has the necessary permissions to access an S3 bucket.
   List the contents of the S3 bucket:
   ```
   aws s3 ls s3://<bucket-name>
   ```
   
   This is my bucket name with command
   
   ```
   aws s3 ls s3://cloudfoxable-bastion-9hypj
   ```
   
   Then i Found a flag.txt file then i copied that file and found the flag name by using command
   
   ```
   aws s3 cp s3://cloudfoxable-bastion-9hypj/flag.txt -

   ```

   I found the flag that is `{FLAG:bastion::ifYouHaveAccessToAnEC2YouHaveAccessToItsIamPermissions}` Then i paste it into challenge site and got success message

## Reflection  
### What was your approach?  
To solve the issue I utilized a step-by-step method where I analyzed the Terraform error before implementing a defined debugging framework to fix it. I proceeded beyond the IP address issue by inputting my IP address manually until the problem was resolved. The application of Terraform led me to use Cloudfox which helped me locate the instance ID to perform flag retrieval through the instructed steps.

### What was the biggest challenge?  
The main obstacle occurred during my attempt to automatically retrieve the IP address through Terraform. I was at first confused because the USER_IP variable displayed HTML data instead of showing my public IP address. My extensive effort to resolve issues led me to find out that https://ifconfig.me provided an unexpected response.

### How did you overcome the challenges?  
I achieved resolution by obtaining my public IP through curl ifconfig.me before entering it directly into the Terraform variables. Manually providing public IP to Terraform solved the dynamic fetching issue thus enabling the successful completion of the process.

### What led to the breakthrough?  
The resolution became possible when I paused to inspect the variable resolution process. The problem stemmed from the operation that fetched public IPs because ifconfig.me delivered HTML content instead of showing the actual IP address. I discovered that avoiding dynamic fetch and using the IP value manually solved the problem to let me move forward.

### On the blue side, how can the learning be used?  
Working with cloud infrastructure proved the critical need for validating external services during my experience. Automation pipelines avoid issues by using secure and dependable dynamic input processes to retrieve public IP addresses. Security demands that you check and verify external data because improper or incorrect content delivery might create unexpected issues. The detection of potential weaknesses depends on routine checks between Terraform configurations and error logs together with external service interactions to strengthen overall stability as well as security in the environment.
   

