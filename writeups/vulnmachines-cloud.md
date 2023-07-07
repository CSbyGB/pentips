# Vulnmachines - Cloud Labs

## Misconfigured Bucket

![Misconfigured Bucket Lab](../.res/2023-04-29-16-20-02.png)  

- When launching the lab we end up on this page

![landing](../.res/2023-04-29-16-23-20.png)  

It is hosted on an AWS S3 bucket `vnm-sec-aws`.  
Googling around we find a few interesting articles and we can try this command with the aws cli `aws s3 ls s3://vnm-sec-aws --no-sign-request`  
However we get an access denied  

![Access denied](../.res/2023-04-29-16-38-22.png)  

> We need to create an aws account and get access ID and secret access this way we will be able to use this account for our test.
> Check out [here](../cloud/aws.md) how to do so  

Let's try this `aws s3api get-bucket-acl --bucket <Bucket_Name>`  
`aws s3api get-bucket-acl --bucket vnm-sec-aws`  

![get acl](../.res/2023-07-07-16-24-33.png)

This means that all authenticated users (globally) can read the bucket.

Sounds good let's try to list the content

### COMING SOON

## Special Policy Bucket

We end up here:  

![website](../.res/2023-07-07-10-15-02.png)  

Let's use nslookup on the IP  

![nslookup](../.res/2023-07-07-10-16-00.png)  

### COMING SOON
