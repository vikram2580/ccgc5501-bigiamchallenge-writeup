# Challenge 3
Its a Secret

## Solution

1. **User and Profile**:  
   To verify the AWS user and profile I was using, I ran the following command:
   ```
   aws --profile cloudfoxable sts get-caller-identity
   ```
 The output confirmed that the correct user, ctf-starting-user, was in use:

   ```
{
    "UserId": "AIDAR7HWX7DJBSEPI35Y7",
    "Account": "135808940242",
    "Arn": "arn:aws:iam::135808940242:user/ctf-starting-user"
}
   ```

2. **Secrets**:  
   Using CloudFox I ran a secret-scanning operation on the environment through the command input.
   ```
   cloudfox aws -p cloudfoxable secrets -v2
   ```
   The output the above command shows several secrets and parameters.
   There is one secret `/cloudfoxable/flag/its-a-secret` that I am looking for.

   
 | Service       | Region   | Name                                   | Description                       |
 |---------------|----------|----------------------------------------|-----------------------------------|
 | SecretsManager| us-west-2| database-secret                        |                                   |
 | SecretsManager| us-west-2| DomainAdministrator-Credentials        |                                   |
 | SecretsManager| us-west-2| SegueFlag                              |                                   |
 | SecretsManager| us-west-2| my-app-secret                          | Secure secret for sensitive data  |
 | SSM           | us-west-2| /cloudfoxable/flag/executioner         |                                   |
 | SSM           | us-west-2| **/cloudfoxable/flag/its-a-secret**    |                                   |
 | SSM           | us-west-2| /cloudfoxable/flag/its-another-secret  |                                   |
 | SSM           | us-west-2| /cloudfoxable/flag/lambda-sqs          |                                   |
 | SSM           | us-west-2| /production/CICD/root                  |                                   |
 | SSM           | us-west-2| /production/database/password          |                                   |
 | SSM           | us-west-2| /production/database/username          |                                   |
 | SSM           | us-west-2| /staging/database/password             |                                   |
 | SSM           | us-west-2| /staging/database/user                 |                                   |



4. **Locate the Key Secret:**:  

   In the output I located the primary secret flag /cloudfoxable/flag/its-a-secret. CloudFox kept the secrets access command present in the following file:

   ```
   [secrets][cloudfoxable] Loot written to [/home/vikram/.cloudfox/cloudfox-output/aws/cloudfoxable-135808940242/loot/pull-secrets-commands.txt]
   ```

5. **Verify Access Permissions:**:  

   I performed the following command to verify my access permission to the secret.
   ```
   cloudfox aws -p cloudfoxable permissions --principal ctf-starting-user -v2
   ```
   The verification displayed that user ctf-starting-user could access secret secrets_prod_by_starting by ssm:GetParameter permission.

   
  | Type | Name           | Policy  | Policy Name        | Effect | Action        | Resource                                                                 | Condition |
  |------|----------------|---------|--------------------|--------|---------------|-------------------------------------------------------------------------|-----------|
  | User | ctf-starting-user | Managed | its-a-secret-policy | Allow  | ssm:GetParameter | arn:aws:ssm:us-west-2:135808940242:parameter/cloudfoxable/flag/its-a-secret | No        |


6. **Fetch the Secret Value:**:  
   The execution of this AWS CLI command retrieved the secret value after decryption using the previously confirmed permission setup.
   ```
   aws --profile cloudfoxable --region us-west-2 ssm get-parameter --with-decryption --name /cloudfoxable/flag/its-a-secret
   ```

   The output given below has the flag vcalue:
```
   {
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-a-secret",
        "Type": "SecureString",
        "Value": "FLAG{ItsASecret::IsASecretASecretIfTooManyPeopleHaveAccessToIt?}",
        "Version": 1,
        "LastModifiedDate": "2025-03-05T22:17:39.318000-05:00",
        "ARN": "arn:aws:ssm:us-west-2:135808940242:parameter/cloudfoxable/flag/its-a-secret",
       "DataType": "text"
     }
  }
 ```
 
   The flag value:  
   `FLAG{ItsASecret::IsASecretASecretIfTooManyPeopleHaveAccessToIt?}`


## Reflection

* **What was your approach?**

  The biggest challenge was ensuring that the user had the proper permissions to access the secret.
  Without the right permissions, accessing the sensitive information would have been impossible.
  
* **What was the biggest challenge?** 

  The main obstacle involved ensuring the user held enough permissions to view the secret information.
  Proper access rights prevented the retrieval of sensitive information because they were essential for obtaining permissions.
  
* **How did you overcome the challenges?** 

  Proceeding with the retrieval process started with checking the user permissions through CloudFox.
  Once I verified that `ctf-starting-user` possessed the `ssm:GetParameter` permission I continued with the secret retrieval process.

* **What led to the breakthrough?**

  My game changed when I managed to acquire the CloudFox secret list followed by securing the flag-containing entry.
  I verified the appropriate secret then used the required command for accessing it after confirming the permissions.

* **On the blue side, how can the learning be used in future?**

  The acquired experience helps prepare for upcoming cloud security assessments as well as CTF challenges.
  The ability to use CloudFox together with AWS CLI for finding and interacting with secrets while managing permissions becomes important in red and blue team operations.
  Successful completion of this operation highlights the necessity of proper control mechanisms for secret management along with access control in cloud systems.
  
