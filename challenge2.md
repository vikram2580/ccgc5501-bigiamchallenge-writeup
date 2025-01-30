# Challenge number
2

## Challenge Statement

bigiamchallege-2 

Analytics
We created our own analytics system specifically for this challenge.
We think it's so good that we even used it on this page.
What could go wrong?

## IAM Policy

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "sqs:SendMessage",
                "sqs:ReceiveMessage"
            ],
            "Resource": "arn:aws:sqs:us-east-1:092297851374:wiz-tbic-analytics-sqs-queue-ca7a1b2"
        }
    ]
}

```

### write a short analysis about the IAM policy here

This policy allows anyone *(Principal: "*")* to send and receive messages from the SQS queue *(wiz-tbic-analytics-sqs-queue-ca7a1b2)* and actions allowes are sqs:SendMessage and sqs:ReceiveMessage it means that anyone can read and inject messages into the queue but they cannot delete, modify the queue.


## Solution

To solve this issue, I start by reviewing the SQS receive message documentation provided by AWS SQS *https://docs.aws.amazon.com/cli/latest/reference/sqs/receive-message.html*, I found the CLI command for receiving the message from that link: *aws sqs receive-message --queue-url https://sqs.us-east-1.amazonaws.com/80398EXAMPLE/MyQueue --attribute-names All --message-attribute-names All*. I copied and pasted this command into the CLI, then modified the URL from the IAM policy. Upon executing this command, I received a message in the body it has a URL, which I copied and pasted into the browser. After that, I took the flag and pasted it into the challenge-2 input box, and I got my success message. 


## Reflection
The main challenge was to read the messages quickly before someone erased them and Understanding how easily anyone could access these messages represented the main breakthrough in acknowledging a major security threat. AWS needs to solve this problem by restricting system access to authorized personnel while simultaneously protecting data through encryption and implementing message-reading logs. The security challenge demonstrates the dangerous consequences of excessively open IAM policies for cloud systems.

