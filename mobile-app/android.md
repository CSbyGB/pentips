# Android Application Hacking

- **Check out my talk and its resources about Android application hacking [here](../talks/android-app.md)**

## ADB

### Install apk

- Once the emulator is launched we can use adb with `adb shell`
- Install without adb shell:

```
adb install ../../../path-ubuntu/vers/fichier/file.apk
```

- The application can then be found in the app list 

## Burp

### Configure Burp with Android Studio

{% embed url="https://www.youtube.com/embed/cT5GZPyOKuc" %} Configure Burp {% endembed %} 

## Decompile code

- Bytecodeviewer 
  - launch with `java -jar` and open apk
- `jadx-gui `
- APKLab (add on visual studio Code)

## MobSF

Launch mobsf:

```bash
/opt/Mobile-Security-Framework-MobSF $ sudo ./run.sh 127.0.0.1:4444
```

## Static Analysis

### Android manifest

- It is located in "resources".  
This file is where developers will describe the essential information of the application. As stated in the Android documentation for developers:
"Every application project must have an AndroidManifest.xml file (with this exact name) at the root of the project's source set. The manifest file describes your app's essential information to the Android build tools, the Android operating system and Google Play."  
[See Reference](https://developer.android.com/guide/topics/manifest/manifest-intro)  

It will be used to mention the application package name, the application components, the permissions and the hardware and software that the application needs.
Note that it is also useful to check which API we need to test the application by looking at the `minSDKVersion`.  
Since the purpose of permissions is to protect the privacy of the user, you can see here some permissions that should not be allowed, depending of course on the purpose of the application.  
See [here](https://developer.android.com/reference/android/Manifest.permission ) full list of permissions.  

#### Allow backup

The allowBackup flag could allow an attacker to take the backup of the application data with adb. So it would be safer to set it to false, as mentioned in the owasp documentation if this attribute is not set, it is enabled by default.  
More details: [OWASP-MTG](https://github.com/OWASP/owasp-mstg/blob/8d67a609ecd095d1bb00aa6a3e211791af5642e8/Document/0x05d-Testing-Data-Storage.md#static-analysis-7)

#### Debuggable

This flag indicates if the application can be debugged. It must be set to false otherwise an attacker could use it to access the application data.
Be aware that sometimes your client will give you the developer version of the application, so this flag will be set to true.
What I usually do in this case is I set it as informative to remind the customer to make sure they change this when they commit the application to production.  
Here is what the flag looks like:  
`android:debuggable="true"`  
See [documentation](https://github.com/OWASP/owasp-mstg/blob/53ebd2ccc428623df7eaf2361d44b2e7e31c05b9/Document/0x05i-Testing-Code-Quality-and-Build-Settings.md#testing-whether-the-app-is-debuggable-mstg-code-2 ) OWASP MTG about this.  

#### Exportable activities

Activities are the screens of the applications. Depending on the application and the activity, some of them should not be exportable because it means that they could be accessible from outside the application.  
Here is an example of code on the pivaa application with exportable activities:  
```
  <service android:name="com.htbridge.pivaa.handlers.VulnerableService" android:protectionLevel="dangerous" android:enabled="true" android:exported="true"/>
        <receiver android:name="com.htbridge.pivaa.handlers.VulnerableReceiver" android:protectionLevel="dangerous" android:enabled="true" android:exported="true">
            <intent-filter>
                <action android:name="service.vulnerable.vulnerableservice.LOG"/>
            </intent-filter>
        </receiver>
        <provider android:name="com.htbridge.pivaa.handlers.VulnerableContentProvider" android:protectionLevel="dangerous" android:enabled="true" android:exported="true" android:authorities="com.htbridge.pivaa" android:grantUriPermissions="true"/>
```

### General tips

- Check Strings.xml
- Try to enumerate database
    - See [Firebase Enum](https://github.com/Sambal0x/firebaseEnum)
- Enumerate public cloud resources
    - See [Cloud Enum](https://github.com/initstring/cloud_enum)
- Lookup for: Secret keys, passwords, comments, URLs, IP, private keys, any sensitive information that should not be in the code.  
With Jadx-gui it is possible to use the global search to search for specific strings such as: API, API_KEY, password, key, ClientId, ClientSecret, id, AWS, Secret, username, firebase.io, http, https, SQL (or other SQL file extensions)  
See here how to use glbal search with jadx-gui:  
{% embed url="https://www.youtube.com/embed/LDkf_kJ25WI" %} Jadx - Global search {% endembed %}  

When you find a database you can then try to read it with sqlitebrowser to see what is in it.  
See [OWASP](https://github.com/HTBridge/pivaa#cleartext-sqlite-database) about this.  

- Interesting resources for static analysis on [Hacktricks](https://book.hacktricks.xyz/mobile-apps-pentesting/android-app-pentesting#static-analysis)

## Dynamic analysis

### General tips 

- Are screens visible in screenshots or instances with sensitive data? Understand: can I take a picture of a screen with sensitive data and if I switch to another application after being on a screen with sensitive data can I go back via cached instances?
- All the usual OWASP Top 10 web vulns
- Dynamic analysis on [Hacktricks](https://book.hacktricks.xyz/mobile-apps-pentesting/android-app-pentesting#dynamic-analysis)
- Check if third party keyboard extensions are allowed and active keyboard cache on sensitive fields
sensitive. Some malware masquerades as Android keyboard extensions.
- Tapjacking PoC equivalent to Clickjacking can be done with Qark

## Disable Certificate Pinning

- See Alissa Knight's workshop about this:

{% embed url="https://vimeo.com/701552838" %} Alissa Knight: Workshop for Women/Non-Binary in API Security: Bypassing Certificate Pinning in Android Using FRIDA {% endembed %}  

## Tools for IOS and Android

- Rooted android device
- Émulateur Android
    - [Genymotion](https://www.genymotion.com) (free 30 days trial)
- Jailbroken Iphone
    - [Electra](https://coolstar.org/electra)
    - [Chimera](https://chimera.sh)
    - [checkm8](https://github.com/axi0mX/ipwndfu)
- Get the App
    - Google play (we can use apk pull)
    - Apple App Store
    - [apk Pure](https://apkpure.com) 
- Software
    - [Frida](https://www.frida.re)
    - [Fridump](https://github.com/Nightbringer21/fridump)
        - Open source memory dumping tool.
- [Frida-ios-dump](https://github.com/AloneMonkey/frida-ios-dump)
- [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
    - Use with apk and ipa
- Burp
- Wireshark
    - Iphone: connect the device: `rvictl -s <UDID>`
    - Iphone: Start the capture: `sudo tcpdump -i rvi0 -w iphone.pcap
    - Android: Make laptop as hotspot capture traffic
    - Android: Pull capture from a pfSense machine or something similar
    - Android: `adb shell tcpdump -s -s 0`
    - Android: HTTP traffic: `adb shell tcpdump -C -s -s 0 port 80
- [Android SDK Tool](https://developer.android.com/studio/releases/platforms.hml)
    - Adb, Fastboot, System Trace
        - ADB
            - adb shell -  Connect to device
            - adb install - sideload apks
            - adb push - PC to device
            - adb pull - Device to PC
            - adb logcat - commande line took that dumps a log of system messages
- [Android apktool](https://ibotpeaches.github.com/Apktool/)
- [IOS Passionfruit](https://github.com/chaitin/passionfruit)
- Decompiling
    - [jadx](https://github.com/skylot/jadx)
    - [cfr](https://www.benf.org/other/cfr)
    - [Ghidra](https://www.nsa.gov/resources/everyone/ghidra/)
- [Qark](https://github.com/linkedin/qark) dynamic analysis can be use for tapjacking PoC
