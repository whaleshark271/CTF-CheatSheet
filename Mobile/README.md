# Mobile

## Resources
* [Mobile Security Testing Guide](https://mobile-security.gitbook.io/mobile-security-testing-guide/)

## Tools
* [Android Studio](https://developer.android.com/studio)
* [Genymotion](https://www.genymotion.com/): Android emulator
  * [Virtualbox](https://www.virtualbox.org/): Virtualization that Genymotion uses
* Use virtualenv for pip
  * `virtualenv -p python3 <virtualenv_name>`
  * `source <virtualenv_name>/bin/activate`
* [Frida](https://frida.re/docs/home/): A dynamic instrumentation toolkit for hooking into functions and processes
  * `pip install frida-tools`
* [Objection](https://github.com/sensepost/objection): A runtime mobile exploration toolkit (basically a bunch of helpful scripts for Frida) that automate SSL pinning, hooking, biometric bypass, etc.
  * `pip install objection`
* [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF): A static analysis tool for iOS and Android Packages.
* [adb](https://developer.android.com/studio/command-line/adb): A versatile command-line tool that lets you communicate with a device.
  * MacOS: `brew install android-platform-tools`
  * Linux: `sudo apt-get install android-tools-adb`

## Proxy Android Mobile App Through Burp
1. Start emulator with `emulator -avd <device-name> -writable-system`
2. Run the following commands:
   ``` bash
   adb root
   adb shell avbctl disable-verification
   adb disable-verity
   adb reboot
   adb remount
   adb shell "su 0 mount -o rw,remount /system"
   ```
3. **Use bash for the following commands instead of zsh**
   ``` bash
   adb root && adb remount && wget http://127.0.0.1:8080/cert -O burp_wget.crt && openssl x509 -inform der -in burp_wget.crt -out burp_wget.pem && adb shell mkdir -p /data/misc/user/0/cacerts-added/ &&openssl x509 -inform PEM -subject_hash_old -in burp_wget.pem | head -1 | xargs -I{} mv burp_wget.crt {}.0 && adb push *.0 /data/misc/user/0/cacerts-added/ && adb shell ls -1t /data/misc/user/0/cacerts-added/* | head -1 | xargs -I{} adb shell cp {} /system/etc/security/cacerts/ && adb shell chmod 644 /data/misc/user/0/cacerts-added/* && adb shell chown system:system /data/misc/user/0/cacerts-added/* && adb shell chmod 644 /system/etc/security/cacerts/*
   ```
4. [Configure Burp and Mobile Device](https://portswigger.net/support/configuring-an-android-device-to-work-with-burp)
