# Challenge number
1

## Challenge Statement

bigiamchallege-1 -- Buckets of Fun

## IAM Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b/*"
    },
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b",
      "Condition": {
        "StringLike": {
          "s3:prefix": "files/*"
        }
      }
    }
  ]
}

```

### write a short analysis about the IAM policy here

```
This IAM policy allows public access to the S3 bucket thebigiamchallenge-storage-9979f4b and allows read access (s3:GetObject) to download any objects inside the bucket including all files under the files/ directory and also it permits listing the objects in the bucket only for files within the files/ and it does not allow permission to upload, modify, or delete any files. The interesting part here is the open access to files within the files/ directory which contain important informationfor the challenge.

```

## Solution
```
To capture the flag in this challenge I read the IAM policy which have public access to download files from the files/ directory of the S3 bucket and I use the AWS CLI to listing the files with the command ===aws s3 ls s3://thebigiamchallenge-storage-9979f4b/files/===  First I am trying to open logo.png file but its show wrong flag please try again using === aws s3 cp s3://thebigiamchallenge-storage-9979f4b/files/logo.png - === that I use === aws s3 cp s3://thebigiamchallenge-storage-9979f4b/files/flag1.txt - === to download the file and content show on the terminal and then I copy the downloaded contnent and patse it to open ===https://bigiamchallenge.com/challenge/1=== page to verifiy the flags and click on check I got success then my  I completed the challenge. 

```
## Reflection

```
In this challenge my approach was to first understand the IAM policy which have read-only access to files in a specific S3 directory and then I used the AWS CLI to list and download files from the files/ directory and looked for flags. The biggest challenge was understanding the IAM policy restrictions but by focusing on the allowed files/ directory I was able to find the flag. This experience taught me the importance of controlling access to sensitive data. In the blue team context it is importannt to limit public access and use strict IAM policies and look up the access to ensure that sensitive data is protected.

```