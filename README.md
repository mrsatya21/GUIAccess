# GUIAccess
This is a Bash Script to grant GUI access to new standard user created in EC2 macOS instance. 

```Shell Script
#!bin/bash
# Steps to grant GUI access to Standard user (user with non admin rights) in EC2 MacOS
# Run the following command to enable the screensharing feature in the macOS instance
    sudo defaults write /var/db/launchd.db/com.apple.launchd/overrides.plist com.apple.screensharing -dict Disabled -bool false
    sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.screensharing.plist

# Run the following command to set a password for ec2-user. 
# Here the password that I have set is "ec2user"
# You are free to change it as you want
    sudo /usr/bin/dscl . -passwd /Users/ec2-user "ec2user"

# Add a new standard user with password.
# Here the password that I have set for new user 'usertest' is "usertest"
# You are free to change it as you want
    NEWUSER="usertest";FIRST_NAME="user";LAST_NAME="test";PASSWORD="usertest"
    sudo sysadminctl -addUser $NEWUSER -fullName "$FIRST_NAME-$LAST_NAME" -password $PASSWORD

# Add the users SSH public key under the new users home directory -
    sudo su $NEWUSER -c "mkdir -pv ~/.ssh && touch ~/.ssh/authorized_keys" && sudo su $NEWUSER -c "chmod 700 ~/.ssh" && sudo su $NEWUSER -c "chmod 600 ~/.ssh/authorized_keys"
    sudo cp /Users/ec2-user/.ssh/authorized_keys /Users/$NEWUSER/.ssh/authorized_keys

# Allow screensharing access to standard user 
    sudo dseditgroup -o edit -a $NEWUSER -t user com.apple.access_screensharing

# Configure the Apple Remote Desktop setting to allow access fo specific user on macOS
    sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -configure -allowAccessFor -specifiedUsers
    sudo /System/Library/CoreServices/RemoteManagement/ARDAgent.app/Contents/Resources/kickstart -configure -users $NEWUSER -access -on -privs -all -restart -agent -menu
```
