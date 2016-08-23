title: React Native for Android
date: 2015-10-01 13:41:54
tags:
---

It has been several weeks since facebook announced react native for android system. We will go through the environment setup and you finally can write android with JAVASCRIPT instead of java. Let's write once run anywhere!

<!-- more -->

## Installation

### Getting Started[^1]

Install `nodejs`, upgrade brew, and then install required packages.

```
$ brew update
$ brew upgrade
$ brew install watchman
$ brew install flow
$ npm install -g react-native-cli
```

### Android Setup[^2]

1. [Install `JDK`](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

2. Install `Android SDK`

    ```
    $ brew install android-sdk
    ```

    > ### Note
    > I have tried hardly to use the sdk previously installed without brew, but eventually I failed. So, I just reinstalled the fucking sdk again with brew

3. Install android packages

    Use the command

    ```
    $ android
    ```

    to open `Android SDK Manager`

    > ### Note
    > This command only works when you install SDK with brew.

    then check these 3 packages and install them:

    - `Android SDK Build-tools version 23.0.1`
    - `Android 6.0 (API 23)`
    - `Android Support Repository`

4. Export Path

    Append this path to `~/.bashrc`

    ```
    # android sdk path for react native
    export ANDROID_HOME=/usr/local/opt/android-sdk
    ```

## New Project

Go to your projects folder, and use this:

```
$ react-native init AwesomeProject
```

## Running on Device[^3]

For the first time, connect your mobile device with mac, and run this:

```
$ cd AwesomeProject
$ react-native run-android
```

then, there will be an error dialog on the device:

![](error.png?small)

### >= Android 5.0[^4]

If you are using android higher than version 5.0, just run this:

```
$ adb reverse tcp:8081 tcp:8081
```

### < Android 5.0

Sadly I am using android 4.4, but it doesn't matter. There is a way to run with wifi instead of usb:

1. Open context menu -> Select `Dev Settings` -> `Debug server host for device`
2. fill in the `ip address` where you run the command `$ react-native run-android`

### Result

Click `Reload JS`, and I think everything is going fine on your device and the app just shows!

![](result.png?small)

For the other times you want to write react native app, just use

```
$ react-native start
```

to open the dev server and you don't need `$ react-native run-android` to install the app again.

## Development

// TO-DO

## Reference

[^1]: [Getting Started](https://facebook.github.io/react-native/docs/getting-started.html)
[^2]: [Android Setup](https://facebook.github.io/react-native/docs/android-setup.html)
[^3]: [Running on Device](https://facebook.github.io/react-native/docs/running-on-device-android.html)
[^4]: [Using adb reverse](http://facebook.github.io/react-native/docs/running-on-device-android.html#using-adb-reverse)
