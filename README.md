# DroidFS
DroidFS is an alternative way to use encrypted overlay filesystems on Android that uses its own internal file explorer instead of mounting virtual volumes.
It currently only works with [gocryptfs](https://github.com/rfjakob/gocryptfs) but support for [CryFS](https://github.com/cryfs/cryfs) could be added in the future.

<p align="center">
<img src="https://forge.chapril.org/hardcoresushi/DroidFS/raw/branch/master/fastlane/metadata/android/en-US/images/phoneScreenshots/1.jpg" height="500">
<img src="https://forge.chapril.org/hardcoresushi/DroidFS/raw/branch/master/fastlane/metadata/android/en-US/images/phoneScreenshots/2.jpg" height="500">
<img src="https://forge.chapril.org/hardcoresushi/DroidFS/raw/branch/master/fastlane/metadata/android/en-US/images/phoneScreenshots/3.jpg" height="500">
</p>

# Disclamer
DroidFS is provided "as is", without any warranty of any kind.
It shouldn't be considered as an absolute safe way to store files.
DroidFS cannot protect you from screen recording apps, keyloggers, apk backdooring, compromised root accesses, memory dumps etc.
Do not use this app with volumes containing sensitive data unless you know exactly what you are doing.

# Unsafe features
DroidFS allows you to enable/disable unsafe features to fit your needs between security and comfort.
It is strongly recommended to read the documentation of a feature before enabling it.

<ul>
  <li><h4>Allow screenshots:</h4>
  Disable the secure flag of DroidFS activities. This will allow you to take screenshots from the app, but will also allow other apps to record the screen while using DroidFS.
  Note: apps with root access don't care about this flag: they can take screenshots or record the screen of any app without any permissions.
  </li>
  <li><h4>Allow opening files with other applications *:</h4>
  Decrypt and open file using external apps. These apps could save and send the files thus opened.
  </li>
  <li><h4>Allow exporting files:</h4>
  Decrypt and write file to disk (external storage). Any app with storage permissions could access exported files.
  </li>
  <li><h4>Allow sharing files via the android share menu *:</h4>
  Decrypt and share file with other apps. These apps could save and send the files thus shared.
  </li>
  <li><h4>Keep volume open when the app goes in background:</h4>
  Don't close the volume when you leave the app but keep running it in the background. Anyone going back to the activity could have access to the volume.
  </li>
  <li><h4>Allow saving password hash using fingerprint:</h4>
  Generate an AES-256 GCM key in the Android Keystore (protected by fingerprint authentication), then use it to encrypt the volume password hash and store it to the DroidFS internal storage. This require Android v6.0+. If your device is not encrypted, extracting the encryption key with physical access may be possible.
  </li>
</ul>
* Features requiring temporary writing of the plain file to disk (DroidFS internal storage). This file could be read by apps with root access or by physical access if your device is not encrypted.

# Download
You can download the latest version in the Releases section. All APKs from v1.3.0 are signed with my PGP key available on keyservers:

`gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 007F84120107191E` \
Fingerprint: `BD5621479E7B74D36A405BE8007F84120107191E` \
Email: `Hardcore Sushi <hardcore.sushi@disroot.org>`

To verify APKs: `gpg --verify <ASC file> <APK file>`

# Permissions
DroidFS need some permissions to work properly. Here is why:

<ul>
  <li><h4>Read & write access to shared storage:</h4>
  Required for creating, opening and modifying volumes and for importing/exporting files to/from volumes.
  </li>
  <li><h4>Biometric/Fingerprint hardware:</h4>
  Required to encrypt/decrypt password hashes using a fingerprint protected key.
  </li>
  <li><h4>Camera:</h4>
  Needed to take photos directly from DroidFS to import them securely. You can deny this permission if you don't want to use it.
  </li>
</ul>

# Build
Most of the original gocryptfs code was used as is (written in Go) and compiled to native code. That's why you need [Go](https://golang.org) and the [Android Native Development Kit (NDK)](https://developer.android.com/ndk/) to build DroidFS from source.

#### Install Requirements
- [Android Studio](https://developer.android.com/studio/)
- [Android NDK and CMake](https://developer.android.com/studio/projects/install-ndk)
- [Go](https://golang.org/doc/install) (on debian: `$ sudo apt-get install golang-go`)

#### Download Sources
```
$ git clone https://github.com/hardcore-sushi/DroidFS.git
```
Gocryptfs need OpenSSL to work:
```
$ cd DroidFS/app/libgocryptfs
$ wget https://www.openssl.org/source/openssl-1.1.1j.tar.gz
```
Verify OpenSSL signature:
```
$ wget https://www.openssl.org/source/openssl-1.1.1j.tar.gz.asc
$ gpg --verify openssl-1.1.1j.tar.gz.asc openssl-1.1.1j.tar.gz
```
Continue **ONLY** if the signature is **VALID**.
```
$ tar -xvzf openssl-1.1.1j.tar.gz
```

#### Build
First, we need to build libgocryptfs.<br>
For this, we will need to install some dependencies:
```
$ sudo apt-get install libcrypto++-dev libssl-dev pkg-config
```
And also Go dependencies:
```
$ go get golang.org/x/sys/unix golang.org/x/sys/cpu golang.org/x/crypto/hkdf
```
Then, retrieve your Android NDK installation path, usually someting like "/home/\<user\>/AndroidSDK/ndk/\<NDK version\>". We can now start the build process: 
```
$ cd DroidFS/app/libgocryptfs
$ env ANDROID_NDK_HOME="<your ndk path>" OPENSSL_PATH="./openssl-1.1.1j" ./build.sh
 ```
Then, open the DroidFS project with Android Studio.<br>
If a device (virtual or physical) is connected, just click on "Run".<br>
If you want to generate a signed APK, you can follow this [post](https://stackoverflow.com/a/28938286).

# Third party code
Thanks to these open source projects that DroidFS uses:

### Modified code:
- [gocryptfs](https://github.com/rfjakob/gocryptfs) to encrypt your data
### Libraries:
- [Cyanea](https://github.com/jaredrummler/Cyanea) to customize UI
- [Glide](https://github.com/bumptech/glide/) to display pictures
- [ExoPlayer](https://github.com/google/ExoPlayer) to play media files
