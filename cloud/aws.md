# AWS

> We need to create an aws account and get access ID and secret access this way we will be able to use this account for our test.  
> [Here](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-appendix-sign-up.html) is an article on how to get the creds once our account created.

## Connect to a bucket

- If we found a key or buckets during enumeration, we need to check them and see if we can connect to them.

- `sudo apt install awscli` install aws cli
- `aws configure --profile targetname` create a profile for our target
- `aws configure list` check your current configuration
- `aws s3 ls s3://target --profile targetname` ls the content of a bucket
- `aws s3 ls  s3://target --no-sign-request --region us-west-2`

Example with the CTF made available by [flAWS](http://flaws.cloud/)  

![flAWS](../.res/2023-07-07-10-31-49.png)  

## Resources

### Documentation

- [AWScli Documentation](https://aws.amazon.com/fr/cli/)

### Learning resources

#### AWS Pentest

- [My AWS Pentest Methodology by Lizzie Moratti](https://medium.com/@MorattiSec/my-aws-pentest-methodology-14c333b7fb58)
- [AWS on Hacktricks](https://cloud.hacktricks.xyz/pentesting-cloud/aws-security)
- [AWS on Pentest Book](https://pentestbook.six2dez.com/enumeration/cloud/aws)
- [Cloud - AWS Pentest - Payload All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Cloud%20-%20AWS%20Pentest.md)
- [Securing AWS: Discover Cloud vulnerabilities via Pentesting Techniques](https://youtu.be/fg_hey18tio)
- [Hacking the cloud by frichette_n](https://hackingthe.cloud/)
- [AWS Cloud Penetration Testing Explained with Example - Cloud Security Podcast](https://youtu.be/2YZaWoBAH7M)
- [AWS Goat Cloud Pentesting - Cloud Security Podcast](https://www.youtube.com/watch?v=2fClIjVF-Y4)
- [Getting Started with Hacking AWS ECS! - Cloud Security Podcast](https://youtu.be/UUPtnqRgBAQ)
- [GETTING STARTED WITH HACKING AWS CLOUD - Cloud Security Podcast](https://youtu.be/Btl78aP-VHo)
- [Cloud Security - Attacks by Joas Antonio Santos](https://github.com/CyberSecurityUP/Cloud-Security-Attacks)
- [Offensive cloud, AWS by Lutzenfried](https://github.com/lutzenfried/OffensiveCloud/tree/main/AWS)
- [0xd4y by Segev Eliezer with plenty of posts about AWS Pentest](https://0xd4y.com/)
- [Offensive Security AWS Guide - Joas A Santos](https://www.linkedin.com/posts/joas-antonio-dos-santos_offensive-security-aws-guide-activity-7024715135717912576-5nEZ?utm_source=share&utm_medium=member_desktop)
- [Finding Treasures in Github and Exploiting AWS for Fun and Profit — Part 1 - Bhagavan Bollina](https://blog.appsecco.com/finding-treasures-in-github-and-exploiting-aws-for-fun-and-profit-part-1-be5cfadf942)
- [Finding Treasures in Github and Exploiting AWS for Fun and Profit - Part 2 - Bhagavan Bollina](https://blog.appsecco.com/finding-treasures-in-github-and-exploiting-aws-for-fun-and-profit-part-2-8ffefa995439)
- [Pentesting Cloud part 1 "Open to the public" CTF Walkthrough by Pawel Rzepa](https://infosecwriteups.com/pentesting-cloud-part-1-open-to-the-public-ctf-walkthrough-aa4dae59ec4e)
- [AWS Privesc exploring odd features of the Trust Policy](https://medium.com/inside-the-tech-by-softserve/aws-privilege-escalation-exploring-odd-features-of-the-trust-policy-7a970a32861)
- [Cloud Hacking: Hacking Amazon AWS - NahamSec](https://youtu.be/Gq4QLy1-jcc)

#### Vulnerable labs to practice on

- [flAWS](http://flaws.cloud/)
- [flAWS2](http://flaws2.cloud/)
- [CloudGoat - RhinoSecurityLabs](https://github.com/RhinoSecurityLabs/cloudgoat)
- [IAM Vulnerable - Bishopfox (you need to deploy it yourself)](https://github.com/BishopFox/iam-vulnerable)
- [AWSGoat - ine labs (you need to deplot it yourself in your own AWS instance)](https://github.com/ine-labs/AWSGoat)

#### Writeups

- [AWS S3 CTF Challenges by Michael McCambridge](https://n0j.github.io/2017/10/02/aws-s3-ctf.html)

#### Create your lab

- [How to build a Cloud Hacking Lab - Beau Bullock](https://youtu.be/4s_3oNwqImo)

### Tools

- [Cloudfox - Bishopfox](https://github.com/BishopFox/cloudfox)
- [My arsenal of AWS Security tools by toniblyx](https://github.com/toniblyx/my-arsenal-of-aws-security-tools)
- [Pacu - RhinoSecurityLabs](https://github.com/RhinoSecurityLabs/pacu)

#### AWS Security

- [AWS Security Study Plan - Jassics](https://github.com/jassics/security-study-plan/blob/main/aws-security-study-plan.md)
- [Awesome AWS Security - Jassics](https://github.com/jassics/awesome-aws-security)
- [Breaking into Cloud Security by Nick Jones](https://www.nojones.net/posts/breaking-into-cloudsec)
- [Awesome Cloud Security by NextSecurity](https://github.com/NextSecurity/Awesome-Cloud-Security)
- [Cloud Security Vulnerabilities - Ashish Rajan](https://github.com/hashishrajan/cloud-security-vulnerabilities)
- [AWS Security Notes by Segev Eliezer (0xd4y)](https://github.com/0xd4y/Notes/tree/main/AWS)
- [AWS Security Checklist](https://awscheck.netlify.app/)