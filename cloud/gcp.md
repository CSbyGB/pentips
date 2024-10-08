# Pentest - Google Cloud Platform

## Understand GCP

- Here is a fun way to grasp the core concepts of GCP (thanks Ygor Cappai for putting all the images in on PDF).  
{% embed url="https://github.com/CSbyGB/pentips/blob/main/.res/GCP-cloudgirldev.pdf" %} The Cloud Girl - Google Cloud Platform {% endembed %}  
- You can get CloudGirl full book [here](https://thecloudgirl.dev/index.html):

## Checklist

- [ ] Enumeration  
    ![image](https://user-images.githubusercontent.com/96747355/177999765-e0de4a51-7505-4fd3-87aa-f63ebadce915.png)  

> *Source: [Nettitude](https://blog.nettitude.com/cloud-penetration-testing-an-essential-guide-nettitude)*

- [ ] Man In the Middle Attack 
- [ ] Access Level Control (Leveraging Permissive IAM Roles and Permissions, Lack of Role-Based Access Controls (RBAC), ...)
  - Enumerate access to Cloud Services for the current IAM role
  - Create new Cloud users
  - Extract credentials from meta­data, configuration files, environment variables, and logs
  - Clone databases and instances to access information stored in snapshots
- [ ] Misconfigured In-bound ports  
- [ ] Over permissive Storage Buckets  
- [ ] Logging and Monitoring  
- [ ] Try to Escape from the GCP sandbox environment  
- [ ] Try to exploit publicly Exposed Services (access keys, tokens, buckets, ...)
- [ ] Check for the usual user creds compromission (cred stuffing, password spray, ...)

## Practice

- Check out [six2dez gitbook here](https://pentestbook.six2dez.com/enumeration/cloud/gcp) with many useful tools and commands for GCP pentest
- If you want to practice check out [GCP-GOAT here](https://github.com/JOSHUAJEBARAJ/GCP-GOAT)
- Take so time also to check out [this amazing tutorial on privilege escalation and post exploitation tactics in Google Cloud Platform environments by Chris Moberly](https://about.gitlab.com/blog/2020/02/12/plundering-gcp-escalating-privileges-in-google-cloud-platform/)

## Tools

- If you are in restricted environment and can not install any other tools you can check out [GCP IAM Analyzer](https://cloud.google.com/sdk/gcloud/reference/asset/analyze-iam-policy)
  - `gcloud asset analyze-iam-policy --organization="123456" --permissions="iam.serviceAccounts.actAS, iam.serviceAccounts.getAccessToken, iam.serviceAccounts.getOpenIdToken, Iam.serviceAccounts.implicitDelegation, iam.serviceAccounts.signBlob, iam.serviceAccounts.signJwt"` find all members that can impersonate a service account from the Organization level
- [gcploit](https://github.com/dxa4481/gcploit)
- [IAAS permission mining](https://github.com/netskopeoss/iaas_permission_mining)
- [Hayat](https://github.com/DenizParlak/hayat)
- [GCP Firewall Enum - Generate targeted port scans for Compute Instances exposed to the internet](https://gitlab.com/gitlab-com/gl-security/threatmanagement/redteam/redteam-public/gcp_firewall_enum)
- [GCP Enum - Most of the enumeration commands in this blog, consolidated to a single script](https://gitlab.com/gitlab-com/gl-security/threatmanagement/redteam/redteam-public/gcp_enum)
- [GCP misc - Various tools for attacking GCP environments](https://gitlab.com/gitlab-com/gl-security/threatmanagement/redteam/redteam-public/gcp_misc)

## Resources

{% embed url="https://blog.nettitude.com/cloud-penetration-testing-an-essential-guide-nettitude" %} Nettitude - Cloud Penetration Testing - An Essential Guide {% endembed %}  
{% embed url="https://book.hacktricks.xyz/cloud-security/gcp-security" %} Cloud Security - GCP - Hacktricks {% endembed %}  
{% embed url="https://desi-jarvis.medium.com/gcphound-a-swiss-army-knife-offensive-toolkit-for-google-cloud-platform-gcp-fb9e18b959b4" %}gcpHound : A Swiss Army Knife Offensive Toolkit for Google Cloud Platform (GCP) - 
Madhav Bhatt  {% endembed %}  
{% embed url="https://youtu.be/QIXZ0DS9arc" %}Permission Mining in GCP - Colin Estep  {% endembed %}  
{% embed url="https://fwdcloudsec.org/presentations/2021/Permission_mining_GCP.pdf {% endembed %}  
{% embed url="https://youtu.be/Ml09R38jpok" %} Compromise any GCP Org Via Cloud API Lateral Movement and Privilege Escalation: Blackhat/Defcon 2020 {% endembed %}  
{% embed url="https://github.com/allisonis/gcp-iam-sentinel-examples" %} Google Cloud Platform IAM Sentinel Policies  {% endembed %}  
{% embed url="https://services.google.com/fh/files/misc/google-cloud-security-foundations-guide.pdf" %} Google Cloud security foundations guide{% endembed %}  
{% embed url="https://user-images.githubusercontent.com/96747355/178763017-8938e7db-2b27-44d0-899c-f3c78b8a4afd.png" %}Google Cloud Developer Cheat Sheet{% endembed %}  
{% embed url=https://thecloudgirl.dev/" %}The Cloud Girl - Illustrations about GCP{% endembed %}  
