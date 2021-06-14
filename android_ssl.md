To intercept https traffic from your non rooted phone : 

- Decompile your app (using apkstudio for example)
- Create file `/res/xml/network_security_config.xml` with following content:
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config>
        <trust-anchors>
            <certificates src="system" />
            <certificates src="user" />
        </trust-anchors>
    </base-config>
</network-security-config>
```
- Inside `androidmanifest.xml` : 
```xml
<application android:networkSecurityConfig="@xml/network_security_config" .....>
```
- Generate keys to sign apk: `"%JAVA_HOME%/bin/keytool" -genkey -alias keys -keystore keys -keyalg RSA -keysize 2048 -validity 10000`
- Sign and compile with apkstudio

- Now the app can use user installed certs for SSL communications
- To intercept the traffic using burp : 
```sh
adb shell settings put global http_proxy localhost:3333
adb reverse tcp:3333 tcp:8080
adb shell settings put global http_proxy :0
```
- All the traffic from the phone is rooted to the port 3333 of the phone, all the traffic from port 3333 of the phone is now rooted to the port 8080 of the computer running adb, make Burp listen to the port 8080 on all interfaces :)
