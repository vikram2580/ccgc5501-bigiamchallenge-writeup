# Challenge number

1

## Challenge Statement

The challenge statement that you are working on

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
* What do I have access to?
* What don't I have access to?
* What do I find interesting?
* etc
```

## Solution

Detailed steps to the flag

## Reflection

- What was your approach?
- What was the biggest challenge?
- How did you overcome the challenges?
- What led to the break through?
- On the blue side, how can the learning be used to properly defend the important assets?
