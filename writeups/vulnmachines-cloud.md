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

![ls bucket](../.res/2023-07-07-16-43-17.png)  

The a folder seems interesting  

It goes like this until letter p and in the p folder we have a flag file!!  

![flag](../.res/2023-07-07-16-45-41.png)  

We can cp it to our local folder `aws s3 --profile gabrielle cp s3://vnm-sec-aws/a/b/c/d/e/f/g/h/i/j/k/l/m/n/o/p/flag.txt .`  

![encoded flag](../.res/2023-07-07-16-48-32.png)

We now need to decode the flag which is in ROT47. We can use Cyberchef to decode it.  

![ROT47](../.res/2023-07-07-17-15-13.png)  

## Public Bucket

![public bucket challenge](../.res/2023-07-07-17-29-33.png)

We have this landing page  
![public bucket](../.res/2023-07-07-17-29-21.png)

- Let's ls the bucket with this command `aws s3 ls s3://vnm-sec-bucket --no-sign-request`  

![ls public bucket](../.res/2023-07-07-17-40-59.png)

We can now try to cp the flag file `aws s3 cp s3://vnm-sec-bucket/flag.txt --no-sign-request .`  

It works and we get the flag!

![flag](../.res/2023-07-07-17-42-50.png)

## Special Policy Bucket

![Specila Policy challenge](../.res/2023-07-07-17-16-15.png)

We end up here:  

![website](../.res/2023-07-07-10-15-02.png)  

Let's use nslookup on the IP  

![nslookup](../.res/2023-07-07-10-16-00.png)  

### COMING SOON
