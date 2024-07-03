# Mobile Hacking Lab - Strings

![Strings](../.res/2024-06-23-17-40-42.png)  

- [Link to lab on Mobile Hacking Lab](https://www.mobilehackinglab.com/course/lab-strings)

## Instructions

Welcome to the Strings Challenge! In this lab,your goal is to find the flag. The flag's format should be "MHL{...}". The challenge will give you a clear idea of how intents and intent filters work on android also you will get a hands-on experience using Frida APIs.  

### Hints and Rules

- Reverse engineer the application and search for exported activities.  
- Understand the code and find a method to invoke the exported activity.  
- Utilize Frida for tracing or employ Frida's memory scanning.  
- Don't have to spend time on static analysis of the Android library, as the code is obfuscated.  
- The flag follows the format "MHL{...}".  
- Do not attempt to patch the application.  

## Process

### Launching the app

![Strings](../.res/2024-06-23-18-13-19.png)

### Checking out the manifest for exported activities

- Let's connect with openvpn and pull the app from the machine to examine it locally.
- `adb shell pm list packages -f | grep com.mobilehackinglab.challenge` we list the package and grep on the one we are looking for
- `adb pull /data/app/~~p_54bM-A63QkAZxdaHHILQ==/com.mobilehackinglab.challenge-AfKKIwpsKTgv5lPYwRWv0Q==/` we pull the folder from the result of our previous command
- `cd com.mobilehackinglab.challenge-AfKKIwpsKTgv5lPYwRWv0Q\=\=/` we go locally into the folder
- `jadx-gui base.apk` we open the app with jadx-gui
- Checking out the manifest. With a search on `android:exported="true"` we find 2 exported activities. The first one is the main and the other is just Activity2.  

![Exported Activities](../.res/2024-06-23-18-10-26.png)  

The following snippet in the manifest in the intent of Activity2 `<data android:scheme="mhl" android:host="labs"/>` is really interesting.  
Which mean we will probably try something like `adb shell am start "mhl://labs/<SOMETHING_HERE>" com.example.package/.className`.  
Let's dig in the code because as said in the instructions we need to understand what happens.  
We are going to look at the following files:  

- MainActicity
- Activity2

### Understanding the MainActivity

I am starting with this class because is the main screen of the app. Its primary function is to set up the user interface and store the current date in the app's shared preferences.

- The method `stringFromJNI()` is a native method it will fetch some text from a library and display it. You can find more info on this method [here in the offical Android Developer documentation](https://developer.android.com/ndk/samples/sample_hellojni)
- `onCreate` Method: it is called when the activity is created. It setups the UI. It then fetches a libray using the mehod mentioned above and will display it in a TextView on the screen.
- One interesting method here is `KLOW`: it sets the current date in shared preferences. 
  SharedPreferences is an interface in Android that provides a simple way to store small amounts of key-value pair data. This data persists across user sessions, meaning it remains available even if the app is closed or the device is restarted.  
  Here, the method gets an editor for the shared preferences named `DAD4` (an `Editor` is an interface used for modifying values in a SharedPreferences object).  
  It saves the current date (formatted as dd/MM/yyyy) under the key UUU0133.  
  It applies the changes, ensuring the date is saved.
  What's interesting also is that this method is not automatically called when the activity starts; it needs to be called explicitly if we want to set the date.

#### Comprehensive walkthroug of MainActivity

1. When MainActivity Starts:
   - The UI is set up using data binding.
   - A string from a native library is displayed on the screen.
2. If KLOW is Called:
   - The current date is saved in shared preferences under the key UUU0133.

### Understanding Activity2

- It will first set up the user interface
- The method `onCreate` is called when the activity is first created
- The method `setContentView` will set the layout for the activity
- When the Initialization is done (as describe above), the activity retrieves a value from shared preferences and compares it with today's date.
  - `u_1`: Retrieves a value stored under the key UUU0133.
  - `isActionView`: Checks if the intent action is VIEW.
  - `isU1Matching`: Checks if u_1 matches today's date (formatted by cd()).
  - If any of these checks fail, the activity exits.
- The activity validates the data in the intent's URI
  - `uri`: Retrieves the URI from the intent.
  - Checks if the URI scheme is `mhl` and the host is `labs`.
  - If the URI does not match these criteria, the activity exits.
- The activity decodes the last path segment of the URI and decrypts it.
  - `decodedValue`: Decodes the last segment of the URI using Base64.
  - `ds`: Converts the decoded value to a string.
  - `bytes`: Converts the secret key to bytes. We can see here that the secret key is hardcoded `your_secret_key_1234567890123456`
  - `decrypt`: Decrypts a hardcoded cipher text (`bqGrDKdQ8zo26HflRsGvVA==`) and compares it with `ds`.
  - If the decrypted value matches `ds`, it loads a native library and shows a message (the flag) (Exciting!! 🤪🤪). Otherwise, the activity exits.
- The decryption method uses AES/CBC/PKCS5Padding to decrypt a cipher text
  - `decrypt`: Decrypts the given cipher text using the specified algorithm and key.
- If all conditions are met, the flag is displayed using a native method
  - `getflag`: A native method that retrieves the flag.
  - `Toast`: Displays the flag as a toast message. (A Toast is a way to show a small message to the user. It appears as a small pop-up on the screen and automatically disappears after a short period of time, without requiring any user interaction.)

#### Comprehensive walkthrough of Activity2

1. Initialization
   - The activity sets up the UI
2. Shared Preferences Check
   - Retrieves value UUU0133 from shared preferences DAD4.
   - Compares it with today's date.
3. Intent Action Check
   - Verifies the intent action is android.intent.action.VIEW.
4. URI Check:
   - Ensures the URI scheme is mhl and host is labs.
5. Decode and Decrypt URI:
   - Decodes the URI path segment from Base64.
   - Converts decoded bytes to string.
   - Prepares decryption key and decrypts a hardcoded cipher text.
   - Compares the decrypted value with the decoded string.
6. Exit if Any Check Fails:
   - If any checks fail, the activity exits.
7. Display Flag:
   - If all checks pass, loads a native library and displays the flag using a toast message.

### Comprehensive walkthrough of the full app

1. Launch MainActivity
   - Sets up the UI using data binding.
   - Displays a string from a native library.
   - Optionally, stores the current date in shared preferences using the KLOW method.
2. Launch Activity2:
   - Sets up the UI.
   - Validates shared preferences to check if the stored date matches today's date.
   - Validates the intent action and URI.
   - Decodes and decrypts part of the URI.
   - If all checks pass, displays the flag using a toast message.

### Exploitation

So according to this analysis, in order to get the flag we have to find a way to get the value of the key (remember what happens when the URI checks path, it decrypts the decoded value with a key). This way we would be able to pass the value in the url.  
See the snippet I am referring to below (from the class Activity2)  
![Jadx-gui snippet](../.res/2024-06-24-16-23-46.png)

> Remember we have key `your_secret_key_1234567890123456`  
> And we have cipherText `bqGrDKdQ8zo26HflRsGvVA==`

I know I should probably use Go or Python, but I just love bash 🤣🤣 So I will make a bash script to solve this.  

## STAY TUNED! :D

![Coming soon](../.res/2024-07-03-16-57-38.png)  
