# Cloudfoxable Challenge 4 
Its Another Secret

## Solution

Step 1: I first created a new profile in AWS so that I could assume the Ertz role. I updated my AWS config file `~/.aws/config` with the following changes:

```
[profile ertz]
region = us-west-2
role_arn = arn:aws:iam::135808940242:role/Ertz
source_profile = cloudfoxable
```

Step 2:  I checked if I could assume the Ertz role by running this command:

```
aws --profile ertz sts get-caller-identity
```

I got successfully output of assuming the role:

```
{
    "UserId": "AROAR7HWX7DJPPDCSXC6H:botocore-session-1743043615",
    "Account": "135808940242",
    "Arn": "arn:aws:sts::135808940242:assumed-role/Ertz/botocore-session-1743043615"
}
```

Step 4: I run the command given below to list the secrets accessible

```
aws --profile ertz secretsmanager list-secrets
```

It give me the error 
The error indicates that the assumed role Ertz does not have permission to list secrets in Secrets

```
An error occurred (AccessDeniedException) when calling the ListSecrets operation: User: arn:aws:sts::135808940242:assumed-role/Ertz/botocore-session-1743043615 is not authorized to perform:
secretsmanager:ListSecrets because no identity-based policy allows the secretsmanager:ListSecrets action
```

Step 5: To check the permissions of the Ertz role, I used the cloudfox tool command given bleow which specifying the ertz principal and this allowed me to investigate the policies associated with the role and understand its access rights.

```
cloudfox aws -p cloudfoxable permissions --principal ertz -v2
```

The output:

```
| Type │ Name │ Policy  │        Policy Name        │ Effect │      Action      │                                     Resource                                      │ Condition │
├──────┼──────┼─────────┼───────────────────────────┼────────┼──────────────────┼───────────────────────────────────────────────────────────────────────────────────┼───────────┤
│ Role │ Ertz │ Managed │ its-another-secret-policy │ Allow  │ ssm:GetParameter │ arn:aws:ssm:us-west-2:135808940242:parameter/cloudfoxable/flag/its-another-secret │ No 
```

This output shows  showed that the Ertz role had permission to perform the `ssm:GetParameter` action on the cloudfoxable secret. This indicated that while it couldn't list all secrets, it had specific access to the cloudfoxable secret.

Step 6: As we leran that we need to run the command to retrieve the value of a specific encrypted parameter `/cloudfoxable/flag/its-another-secret` from `AWS SSM Parameter` Store, 
using the ertz profile with decryption enabled. This allows you to access the secret stored in the parameter.

```
aws --profile ertz --region us-west-2 ssm get-parameter --with-decryption --name /cloudfoxable/flag/its-another-secret
```

Finally, the output give the flag value:

```
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-another-secret",
        "Type": "SecureString",
        "Value": "FLAG{ItsAnotherSecret::ThereWillBeALotOfAssumingRolesInThisCTF}",
        "Version": 1,
        "LastModifiedDate": "2025-03-05T22:17:44.654000-05:00",
        "ARN": "arn:aws:ssm:us-west-2:135808940242:parameter/cloudfoxable/flag/its-another-secret",
        "DataType": "text"
    }
}
```
The value of secret flag is `FLAG{ItsAnotherSecret::ThereWillBeALotOfAssumingRolesInThisCTF}`

## Reflection

* **What was your approach?**

 The process began with role assumption before using AWS CLI to check permissions followed by taking advantage of the permitted actions and roles to access the secret.

* **What was the biggest challenge?**

  The primary challenge in this process involved understanding which permissions were assigned to the Ertz role. I initially believed Secrets Manager access was needed yet an error proved that the role did not have proper authorization for this function.

* **How did you overcome the challenges?**

  I solved the challenge by utilizing cloudfox to view the permissions of the Ertz role permissions. The role possessed `ssm:GetParameter` privilege that gave me access to retrieve the secret through AWS Systems Manager.

* **What led to the breakthrough?**

  I reached a critical understanding that the role needed `ssm:GetParameter` permission to proceed. My understanding led me to pull the parameter from AWS Systems Manager to obtain access to the flag.
  The procedure proved how crucial it is to understand role permissions while using appropriate AWS services for retrieving confidential information.

* **On the blue side, how can the learning be used in the future?**

  Undergoing this experience taught me about the importance of knowing every role permission detail before examination. My future usage of cloudfox tools will become more effective for analyzing permissions because it helps me find hidden secrets or flags much more efficiently.
  The method aims to simplify data changes by reducing time-consuming issues that might occur in future security tests.
