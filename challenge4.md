# Challenge number
4

## Challenge Statement

Admin only?
We learned from our mistakes from the past. Now our bucket only allows access to one specific admin user. Or does it?

## IAM Policy

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebigiamchallenge-admin-storage-abf1321/*"
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebigiamchallenge-admin-storage-abf1321",
            "Condition": {
                "StringLike": {
                    "s3:prefix": "files/*"
                },
                "ForAllValues:StringLike": {
                    "aws:PrincipalArn": "arn:aws:iam::133713371337:user/admin"
                }
            }
        }
    ]
}

```

### write a short analysis about the IAM policy here


The IAM policy controls access to the S3 bucket named *thebigiamchallenge-admin-storage-abf1321*. The policy comprises of two statements. The first statement grants everyone *(Principal": "*")* the permission to read *(s3:GetObject)* any object within the bucket with no conditions. Therefore, any person anywhere, authenticated or not, will be able to read anything in the bucket, provided that they know the exact object key. The second statement seeks to limit the ability to list (s3:ListBucket) what is in the bucket by setting a condition that the requestor ARN should equal arn:aws:iam::133713371337:user/admin. The permission only applies to objects with a prefix of "files/*".
That is the critical failing of this policy. We have successfully restricted who can list the bucket’s contents to the chosen admin user, but there are no restrictions on who can download the bucket’s contents. Since the s3:GetObject action is allowed for *(Principal": "*")*, any user can download the bucket’s contents if they can guess or discover the names of the objects in the bucket. This makes the listing restriction a moot point from a security perspective. While unauthorized users cannot list all the objects available, they can download those objects if they are aware of their names.
The other interesting point here is the *s3:ListBucket* permission is limited to files under the "files/*" prefix only. So even if one was authenticated as admin, they still may not be able to list files outside of that prefix. This indicates an attempt to enforce stricter access control, but the policy ultimately fails to provide it since the public *s3:GetObject* permission takes priority. Summarily, the policy provides limited restrictions on listing permissions, but the bucket objects are not effectively secured because they are publicly readable.


## Solution

The Cloud Solution Architecture course introduces me to S3 buckets and IAM policies learning. My practical on S3 bucket work aligned with the system helped me identify various access control methods. My task involved analyzing an IAM policy while using its configurations.

Step 1: Listing the Bucket Contents
The IAM policy enables the admin user to list buckets through *s3:ListBucket* but only if the objects belong to under the files/* prefix so possibly allowing private viewing of those files if I access them correctly. I execute the following request with the --no-sign-request argument to disable authentication testing:
*aws s3 ls s3://thebigiamchallenge-admin-storage-abf1321/files/ --no-sign-request*

The command give output of two objects.
flag-as-admin.txt
logo-admin.png

Step 2: Retrieving the Flag File
I can download the flag file directly because the access policy enabled public read *s3:GetObject* access to all objects inside the bucket. Again I execute the following request with the --no-sign-request argument to disable authentication testing:
*aws s3 cp s3://thebigiamchallenge-admin-storage-abf1321/files/flag-as-admin.txt - --no-sign-request*

The command give output of flg.
*{wiz:principal-arn-is-not-what-you-think}*

Step 3: Submitting the Flag
The flag value that I obtain in my second step *{wiz:principal-arn-is-not-what-you-think}*i copy paste into the input *Insert Flag Here*  and click on Check after submitting I got success.


## Reflection

I approached this challenge by examining the IAM policy thoroughly to detect any configuration mistakes. From its first impression the policy appeared restrictive because it directly restricted s3:ListBucket access to an admin user with particular conditions. The * s3:GetObject * permission allowed unrestricted download access to all objects from the bucket provided anyone knew their filenames. My analysis prompted me to execute step by step testing procedures evaluating various permission levels. In my initial attempt to access the files in the * "files/*" * prefix via * aws s3 ls * command with * --no-sign-request * I tried to list the files. The two available files named as flag-as-admin.txt and logo-admin.png through my application of the flag. I use * aws s3 cp * to download the flag file after obtaining its location from the listing restriction because public read access remained active.

The main difficulty when performing this task involved reading the detailed operational effects of the IAM restrictions. I initially thought file listing would be totally blocked by the restricted s3:ListBucket access granted only to the admin user. Hands on testing allowed me to identify that "files/*" allowed for listing even though s3:ListBucket access was intended to be restricted to the admin user. Testing required me to confirm that I noticed all secret constraints which could stop an object from being retrieved. I resolved the problems by performing regular command tests and validating which led to the flag retrieval.

The resolution of our problem became possible when I successfully retrieved the file list from "files/*". The security level revealed through the listing function proved that the system restrictions were less strict than initially expected. The flag could be downloaded after obtaining the filenames because the policy allowed public s3:GetObject access. IAM conditions might create an improper security view when other permissions allow too much access. Even though listing was partially restricted this security showed that unauthorized access to objects remained possible.

The defensive exercise explained the importance of sufficiently protecting S3 buckets and IAM policies for proper security. The final error in this implementation became "Principal": "*" for s3:GetObject because it eliminated all listing restrictions. Concerns need to adopt the least privilege principle for S3 bucket permissions through which only approved users and IAM roles should have access. AWS Cloud and bucket policies with denial of public access and strict execution enable organizations to detect unauthorized access attempts while monitoring access logs. Right security configurations must execute two actions: eliminate all s3:ListBucket unauthorized actions while permitting authorized users with proper permissions to access objects. The security improves through the implementation of these security measures which prevents unintentional data exposure to the public domain.
