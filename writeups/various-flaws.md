# flAWS 1 & 2

These labs are a great introduction for AWS Pentest  

- [flAWS](http://flaws.cloud/)
- [flAWS2](http://flaws2.cloud/)

## flAWS 1

### Level 1

![level 1](../.res/2023-07-07-10-45-15.png)

First task is to find the first sub-domain.  

- Let's make a dig command first  

![dig](../.res/2023-07-07-10-46-58.png)  

- Now we can nslookup on one of the IP address  

![nslookup](../.res/2023-07-07-10-55-15.png)  

We now have more details about the s3 bucket, we can use aws cli `aws s3 ls s3://flaws.cloud --no-sign-request --region us-west-2` here we are doing an ls on the bucket and we specify the region.  

![aws cli](../.res/2023-07-07-14-05-23.png)  
If we check the html file we end up on this page  

![secret file](../.res/2023-07-07-14-22-39.png)  

### Level 2

![Level 2](../.res/2023-07-07-14-23-48.png)  

Let's check the bucket but this time with our own user `aws s3 --profile gabrielle ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud`

I litterally pulled my hair here xD.  
I had an access denied `An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied` even with my user, what solved the problem is that I needed to add said user to a group in order for it to have permissions.  
See more about this [here](../cloud/aws.md)  
Once I had done this I tried the command again and got the ls  

![ls](../.res/2023-07-07-16-19-04.png)  

And then again we can access the secret html file and we end up here  

![Access to Level 3 unlocked](../.res/2023-07-07-16-20-25.png)  

### Level 3

![Level 3](../.res/2023-07-15-10-44-18.png)  

Let's try to ls the bucket `//level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/`  

We can see a git folder. This definitely something I would consider in a web pentest, so let's explore it.  

![git](../.res/2023-07-15-11-11-49.png)  

Let's take the full git folder with `aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git/ --profile gabrielle .`  

The commit message is promising  

![commit msg](../.res/2023-07-15-11-25-22.png)  

Ok in order to be able to use `git diff` we need to get the full thing `aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/ --profile gabrielle .`  

Here is what we get with git diff  

![git diff](../.res/2023-07-15-11-43-00.png)  

Here is some exploration we can do with git.  

![git log](../.res/2023-07-15-12-09-12.png)  

![git status](../.res/2023-07-15-12-25-14.png)  

And using git extractor (if we want to go faster) we can find the access_keys!! :D

![git extractor](../.res/2023-07-15-14-11-48.png)  

![access keys](../.res/2023-07-15-14-12-29.png)  

Now we need to configure a new profile with these creds  

![aws configure](../.res/2023-07-15-14-16-30.png)  

Now we just need to list the buckets of our new profile `aws --profile flaws s3 ls`  

![list bucket](../.res/2023-07-15-14-28-35.png)  

We can now access to the next level using the url `http://level4-1156739cfb264ced6de514971a4bef68.flaws.cloud/`  

## Level 4

![Level 4](../.res/2023-07-15-14-38-42.png)  

```txt
access_key AKIAJ366LIPB4IJKT7SA
secret_access_key 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
```

When accessing the provided link we are prompter for credentials  

![Request to new url](../.res/2023-07-15-14-40-39.png)  

The big hint we had here that there was a snapshot made of the EC2.  
Maybe it is publicly accessible.  

Let's list the snapshots for our profile `aws --profile flAWS ec2 describe-snapshots`  

This returned a lots of result. Turns out that these are all the publicly accessible snapshots...

We need to check the account id of our new profile.  

![account ID](../.res/2023-07-15-14-48-53.png)  

Now we can specify the owner ID in order to get our specific snapshot.  

Here is what I got for a snapshot. Not really satisfying  

![snapshot empty](../.res/2023-07-15-15-11-05.png)  

I had to use the hint because I was stuck here and I realized that we needed to specify another region than the one I configured.  
I am not sure how we should figure it out except by trying all the regions one by one until we get something.  
So we can either reconfigure our profile with the region `us-west-2` or specify the region in the commande to list the buckets like this `aws --profile flAWS ec2 describe-snapshots --owner-id 975426262029 --region us-west-2`.  
I just reconfigured my profile.  
And it worked we got the snapshot description below  

![snapshot](../.res/2023-07-15-15-13-41.png)  

Apparently we will need to mount the snapshot in our own AWS account.  

`aws --profile gabrielle ec2 create-volume --availability-zone us-west-2a --region us-west-2  --snapshot-id  snap-0b49342abd1bdcb89`  

Now we need to create and ec2 instance.  
I can only recommend that you checkout executeatwill's writeup for this step.
You can also find it in the AWS chapter of my pentips [here](../cloud/aws.md)  

I personnaly had a few issues because my settings were for another region and so to modify permission I had to delete my current region to move it to another one.  
Long story short, I stopped here for this challenge.  

However I really recommend if you have the same problem and don't want to create another account or delete your current settings that you read the writeup it is really interesting.  

Executeatwill's one is really good but there are a few others available online.  

## Resources

- [Flaws.Cloud Walkthrough Level 1 by Marc Lopez on Youtube](https://youtu.be/-3rtfcAtfw8)
- [Flaws.Cloud Walkthrough Level 2 by Marc Lopez on Youtube](https://youtu.be/IgZw_MKrYeA)
- [Flaws.Cloud Walkthrough Level 3 by Marc Lopez on Youtube](https://youtu.be/3K11OGohuMI)
- [Flaws.Cloud Walkthrough Level 4 by Marc Lopez on Youtube](https://youtu.be/SK-Vm2JqV30)
- [A Beginner's Guide To Exploiting AWS Misconfigurations | Flaws.Cloud Full Walkthrough by Cyberwox](https://youtu.be/Suxqxd74a_Q)
- [Flaws.cloud Walkthrough by Executeatwill](https://executeatwill.com/2022/01/17/Flaws.Cloud-Walkthrough/)
- [Hacking AWS - Flaws.Cloud Walkthrough by Phil Keeble](https://philkeeble.com/cloud/Flaws.Cloud-Walkthrough/)