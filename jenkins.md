# Jenkins

    iOS jenkins CI Code Sign error: No matching provisioning profile found

    1.Ensure the project is building successfully from Xcode to real target.

    -In KeyChain
    2. Copy all the development cretificates & credentials form your user folder to the system folder

    3.Copy all the Provisioning profiles existing in
    ~/Library/MobileDevice/Provisioning Profiles
    to
    Jenkins/Library/MobileDevice/Provisioning Profiles

    sudo chown -R admin:staff xxx
    
Step1. 

Xcode plugin
![Xcode plugin](assets/jenkins/Screen_Shot_2015-12-20_at_18.45.09.png)
FTP plugin
![Xcode plugin](assets/jenkins/Screen_Shot_2015-12-20_at_21.44.49.png)
Step2. 

Generate Item
![Generate Item](assets/jenkins/Screen_Shot_2015-12-20_at_18.42.25.png)

Step3.

Setting
![Setting](assets/jenkins/Screen_Shot_2015-12-20_at_18.46.22.png)
![Setting](assets/jenkins/Screen_Shot_2015-12-20_at_18.38.41.png)
![Setting](assets/jenkins/Screen_Shot_2015-12-20_at_18.47.23.png)