# Mobile Hacking Lab - Lab Config Editor

![Config Editor](../.res/2024-03-14-15-35-40.png)

## Instructions

Welcome to the Config Editor Challenge! In this lab, you'll dive into a realistic situation involving vulnerabilities in a widely-used third-party library. Your objective is to exploit a library-induced vulnerability to achieve RCE on an Android application.

## Process

Let's have a look at the app:  

![Launch the app](../.res/2024-03-14-15-30-18.png)  

From this screen, we click on ConfigEditor  

We have then this screen  

![Screen App](../.res/2024-03-14-15-31-24.png)  

It seems to manipulate yaml files.  
If we try to load the provided file which is a yaml, we get this.  

![loaded yaml](../.res/2024-03-14-16-03-35.png)  

Pull the app to analyse it `adb pull /data/app/~~7nJyEbpomszhvZKacYpBDQ==/com.mobilehackinglab.configeditor-Xlgn336TGw-b6-q5Xmjmyg==/base.apk`  

I decompiled it with apktools `apktool d base.apk`  

![decompile](../.res/2024-03-14-16-18-48.png)

### Manifest file

We see here another reference to yaml.  
But nothing much in terms of libraries.

As it is using yaml, I tried to check for a file with yaml in the name in the decompiled files using `find`.  

![Find](../.res/2024-03-14-16-20-14.png)  

And if we search snakeyaml on Internet, it is a library. So it might be our vulnerable library.  
We find a mention to it in this [tutorial from baeldung](https://www.baeldung.com/java-snake-yaml) and it is suppose to parse yaml file

- [This here](https://bitbucket.org/snakeyaml/snakeyaml/src/master/) seems to be the repo

And the following articles refer to CVE-2022-1471 found on snakeyaml:

- [SnakeYaml: Constructor Deserialization Remote Code Execution](https://github.com/google/security-research/security/advisories/GHSA-mjmj-j48q-9wg2)
- [CVE-2022-1471 SnakeYAML Vulnerability in NetApp Products](https://security.netapp.com/advisory/ntap-20230818-0015/)

# COMING SOON