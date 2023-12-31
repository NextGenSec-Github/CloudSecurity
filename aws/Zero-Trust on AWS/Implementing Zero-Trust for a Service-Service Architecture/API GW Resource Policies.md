## Improve your security posture using API Gateway resource policy

In this task, you'll update the resource policy of your API to combine identity-centric and network-centric controls.

**Steps:**

1. Open the AWS Management Console and navigate to API Gateway.
2. Select your API to secure.
3. Choose `Resource Policy`.
4. Delete the existing resource policy.
5. Add the following policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:*:*:*"
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:*:*:*",
            "Condition": {
                "StringNotEquals": {
                    "aws:PrincipalAccount": "INSERT_AWS_ACCOUNT_ID"
                }
            }
        },
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "execute-api:Invoke",
            "Resource": "arn:aws:execute-api:*:*:*",
            "Condition": {
                "StringNotEquals": {
                    "aws:SourceVpce": "INSERT_APIGW_VPC_ENDPOINT_ID"
                }
            }
        }
    ]
}

