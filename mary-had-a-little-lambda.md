---
description: misc, cloud
---

# Mary had a little lambda

**Platform:** DownUnderCTF

**Difficulty:&#x20;**<mark style="color:yellow;">**medium**</mark>

***

### Description

The Ministry of Australian Research into Yaks (MARY) is migrating their yak catalog to a serverless AWS architecture. Unfortunately, they’ve leaked AWS credentials belonging to an admin user. Your goal is to use this access to uncover their secrets.



### Resolution

The challenge provided the following credentials:

```
[devopsadmin]
aws_access_key_id = AKIA...REDACTED
aws_secret_access_key = ...REDACTED...
region=us-east-1
```

I began by configuring the AWS CLI:

```
aws configure --profile mary
```

Then I listed available Lambda functions:

```
aws lambda list-functions --profile mary --region us-east-1
```

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Only one function was found: `yakbase`&#x20;

So I inspected it.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

From its Location I downloaded the code ZIP from the S3 link and extracted it.



It only had the following python file:

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

The code revealed the function attempts to:

1. Read a secret from AWS SSM Parameter Store: `/production/database/password`
2. Connect to an RDS instance (IP: `10.10.1.1`)
3. Run: `SELECT * FROM bovines`&#x20;



Attempting to invoke the function failed due to lack of `lambda:InvokeFunction` permissions.

I enumerated the user policies.

```
aws iam list-user-policies --user-name devopsadmin --profile mary
aws iam get-user-policy --user-name devopsadmin --policy-name restricted --profile mary

```

Resulting permissions allowed:

* Listing functions
* Getting specific function metadata
* Getting a specific role
* Viewing own policies

But **not** invoking functions or accessing SSM directly.



I retrieved the trust policy of `lambda_role`:

```
aws iam get-role --role-name lambda_role --profile mary
```

it allowed&#x20;

```
"Principal": {
  "Service": "lambda.amazonaws.com",
  "AWS": "arn:aws:iam::487266254163:user/devopsadmin"
}
```

Bingo! This meant I could assume the Lambda role!



Once I discovered that the IAM role `lambda_role` had a **trust relationship** allowing the `devopsadmin` user via its `AssumeRolePolicyDocument`, I realized I could exploit this to escalate privileges.&#x20;



So, I used `sts assume-role` to assume the role and gain additional permissions:

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Using the returned credentials, I created a new profile `lambda_escalated` with full assumed role permissions. I also configured the session token from what we got previously.&#x20;

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Now with elevated privileges, I tried to access the parameter store:

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

WE GOT A VALUE!

This returned the database password which was the challenge flag.



We successfully exploited a trust policy misconfiguration in `lambda_role`. Really enjoyed this challenge!!
