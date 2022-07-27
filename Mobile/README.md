# Mobile

## Tools
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
