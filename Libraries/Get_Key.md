# Cách lấy 1 số key

## key SSL

C:\Program Files\Java\jdk1.8.0_211\bin>

keytool -exportcert -alias androiddebugkey -keystore "keystore_position\debug_keystore.jks" | "D:\Download\openssl-0.9.8k_X64\bin\openssl" sha1 -binary | "D:\Download\openssl-0.9.8k_X64\bin\openssl" base64

## Sha1

Get SHA1 from Android studio.

- Open Android Studio
- Open your Project
- Click on Gradle (From Right Side Panel, you will see Gradle Bar)
- Click on Refresh (Click on Refresh from Gradle Bar, you will see List Gradle scripts of your Project)
- Click on Your Project (Your Project Name form List (root))
- Click on Tasks
- Click on Android
- Double Click on signingReport (You will get SHA1 and MD5 in Run Bar(Sometimes it will be in Gradle Console))
- Select app module from module selection dropdown to run or debug your application   You also need to get google-services.json from firebase console and put into your project.
