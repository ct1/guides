# ADB problems

----------

### ADB Unhauthorized device
1. Delete adbkey and adbkey.pub files.
Rerun the adb server, possibly ask for devices list, and close the server; after that the adbkey file are rebuilt
    ```
    adb kill-server
    adb start-server
    * daemon not running. starting it now on port 5037 *
    * daemon started successfully *
    adb devices
    List of devices attached 

    adb kill-server
    ls ~/.android/
    adbkey  adbkey.pub  androidwin.cfg  avd  cache  repositories.cfg  sites-settings.cf

    rm ~/.android/adbkey
    rm ~/.android/adbkey.pub
    ```

2. Failure [INSTALL_FAILED_UPDATE_INCOMPATIBLE]
Cannot deply file.apk to mobile. Usually it's due to having a signed release version on my phone, then trying to deploy the debug version on top. It gets stuck in an invalid state where it's not fully uninstalled.
    ```
    Get package.id from the build process -> package name (e.g. com.csform.ionic.blue)
    adb uninstall com.csform.ionic.blue
    Success

    ```

3. rxjs
Cannot find module 'rxjs/internal/Observable'.
    ```
    npm install rxjs@6 rxjs-compat@6 --save
    ```

4. Error in background geolocation
if (lastActivity.getType() == DetectedActivity.STILL) {
                        ^
  class file for com.google.android.gms.internal.zzbfm not found
    ```
    uninstall background service
    ```

