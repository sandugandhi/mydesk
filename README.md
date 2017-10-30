
# Our legacy Smartcard with Mac OS X

## Requirements

* Identiv SCR3500A smart card reader
* Gemalto IDPrime .NET smart card 
* Tested for Mac OS X Sierra (10.12.6). Probably, it should work for Mac OS Sierra (10.12), El Capitan (10.11), 
  Yosemite (10.10) and Mavericks (10.9).
* Mozilla Firefox (Tested with the latest version 56.0.2)
* Citrix Receiver

## Installation steps: 

On the "Install Smart Card drivers" page
Click on "Smart Card Package for MyDesk on OSX 10.10.pkg" link to download the .dmg file.
Double click the .dmg file and extract/copy the .pkg file into your ~/Downloads folder. 
(Attempt installing the .pkg which will obviously fail for incorrect Mac OS X version. 
But, it doesn't seem to clean up the failed installation. 
Check out /tmp/ or /Library/Application Support/ folders for example ;)

### Script
```
cd ~/Downloads
pkgutil --expand ./IDGo800.PKCS11.pkg ./IDGo800-unpkg/
cd ./IDGo800-unpkg/myroot.pkg
ls
cat ./Payload | gzip -d - | cpio -id
ls
cp ./usr/lib/libidprimepkcs11.0.dylib ~/
```

### Firefox with PKCS#11 library module and root certificates.

Open Firefox --> Preferences --> Privacy and Security --> Security Devices
Click on "Load" 
Give the path as ~/libidprimepkcs11.0.dylib as the Module filename

####### In case you need the CS Root certificates ##########

On the "Install Smart Card drivers" page
Click on "Smart Card Package for MyDesk on OSX 10.9.pkg" to download the .pkg file
You can attempt installing this .pkg file which will obviously fail.
However, it will give you the extracted root certificates at /Library/Application\ Support/CreditSuisse/. 
Alternatively, you can extract the .pkg using same procedure as above.
Import certificates in Firefox and 'trust' the root certificates.

### Testing notes
* The card may not detected, the first time when the card is inserted in the reader connected to the USB port of your Mac. 
Remove the card and the reader and attempt again. 

### To disable the new CryptoTokenKit library and fallback to Tokend way
```
sudo defaults write  /Library/Preferences/com.apple.security.smartcard DisabledTokens -array com.apple.CryptoTokenKit.pivtoken
sudo defaults write  /Library/Preferences/com.apple.security.smartcard allowSmartCard -boolean YES
```
##### Other settings are optional, I suppose.
```
sudo defaults read  /Library/Preferences/com.apple.security.smartcard
{
    DisabledTokens =     (
        "com.apple.CryptoTokenKit.pivtoken"
    );
    OneCardPerUser = YES;
    UserPairing = NO;
    allowSmartCard = YES;
    checkCertificateTrust = NO;
}
```
#### If FileVault is on, use this command to turn off the automatic login feature:
```
sudo defaults write /Library/Preferences/com.apple.loginwindow DisableFDEAutoLogin -bool YES
```
#### Display login window as list of users
```
System Preferences --> Users & Groups --> Login Option --> Select radio button for 'List of users'
sudo defaults write /Library/Preferences/com.apple.loginwindow SHOWFULLNAME -int 0
```
#### enable smartcard
“sudo security authorizationdb smartcard status” should show that smartcard is enabled for authentication. If not,
```
sudo security authorizationdb smartcard enable
```
######### References and many Thanks ##########

- Ludovic Rousseau

https://ludovicrousseau.blogspot.in/2017/01/macos-sierra-and-legacy-smart-card-login.html

- Richard Purves

http://www.richard-purves.com
