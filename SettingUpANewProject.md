Setting Up Travis and Kif
------------------------

####Requirements:
- Runnable XCode Project with a Unit Test target
- Github Repo with Project

####Add Kif to your project
1. Create a file called `Podfile`
2. Insert this code and replace TargetName with the name of your test target
```
target 'Cast Audio Tests', :exclusive => true do
  pod 'KIF', '~> 3.0', :configurations => ['Debug']
end
```
  - Make sure you don't include this target with any other target list such as 'link_with'
3. Run `pod install` in the terminal in the root of your project.
4. From now on use the .xcworkspace file that was generated

####Setup Travis - Directory Structure
1. Add .travis.yml to the root of your project directory while replacing all of the dubious fields:

```
language: objective-c
env:
  global:
  - APP_NAME="My Application Name that will show up under the app icon"
  - 'CERT_NAME="iPhone Distribution: My Company, Inc (343VE5MT6Z)"'
  - PROFILE_NAME="appstore_com.mycompany.myapp.mobileprovision"
  - PROJECT_FILE_NAME="MyApplication.xcworkspace"
  - CERT_FILE_NAME="MyCert.cer"
  - P12_NAME="MyCert.p12"
before_script:
- openssl aes-256-cbc -k "$ENCRYPTION_SECRET" -in scripts/profile/$PROFILE_NAME.enc -d -a -out scripts/profile/$PROFILE_NAME
- openssl aes-256-cbc -k "$ENCRYPTION_SECRET" -in scripts/cert/$CERT_NAME.enc -d -a -out scripts/cert/$CERT_NAME
- openssl aes-256-cbc -k "$ENCRYPTION_SECRET" -in scripts/cert/$P12_NAME.enc -d -a -out scripts/cert/$P12_NAME
- "./scripts/add-key.sh"
script:
- xctool -workspace "$PROJECT_FILE_NAME" -scheme MyApplicationScheme -sdk iphonesimulator ONLY_ACTIVE_ARCH=NO
- xctool test -parallelize -workspace "$PROJECT_FILE_NAME" -scheme MyApplicationScheme -sdk iphonesimulator ONLY_ACTIVE_ARCH=NO
- xctool -workspace "$PROJECT_FILE_NAME" -scheme MyApplicationSchemeWithoutTests -sdk iphoneos -configuration Release OBJROOT=$PWD/build SYMROOT=$PWD/build 'CODE_SIGN_RESOURCE_RULES_PATH=$(SDKROOT)/ResourceRules.plist'
after_success:
- "./scripts/sign-and-upload.sh"
after_script:
- "./scripts/remove-key.sh"
```
  - You can find CERT_NAME inside of xcode by going to: Build Settings -> Code Signing -> Code Signing Identity -> Release
    - tap on the field to see the list of currently installed certificates by name.
  - CERT_NAME and P12_NAME is the name of the files you're going to create as explained further down.

2. Grab all of the scripts from here https://github.com/cloudspace/ChromeCast-Audio-Sample/tree/master/scripts and place them inside of a folder called `scripts` in the root of your repo

####Setup Travis - Encrypting Sensitive Files
You are going to want to add your public cert, your private key, and your provisioning file to the repo. However, we have to encrypt it since our repo is public.

4. Obtain your cert, p12 (password protected), and matching provisioning file that is targeting the app store (not adhoc; this type does not include beta test permissions)
5. Add your p12 password to the .travis.yml file by typing this in the terminal:

```
travis encrypt "KEY_PASSWORD={password}" --add
```

6. Next, we create a different password and we encrypt our sensitive files with this command in the terminal:

```
openssl aes-256-cbc -k "{password}" -in MyProvisioningFile.mobileprovision -out MyProvisioningFile.mobileprovision.enc -a
```

7. Again, we add the password we used for encrypting the files to the .travis.yml with this command line:

```
travis encrypt "ENCRYPTION_SECRET=\"This is my password with spaces\"" --add
```

8. Move the files to these directories in the root of your project and update PROFILE_NAME, CERT_NAME, P12_NAME in the .travis.yml file.

```
scripts/profile/$PROFILE_NAME
scripts/cert/$CERT_NAME
scripts/cert/$P12_NAME
```

9. Make sure everything runs locally!
