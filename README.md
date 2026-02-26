# mac-mini-for-ai
Mac mini complete setup for AI, feat tailscale, claude, codex, openclaw, expo, node, agents and many other dependencies


# Dependencies

brew install tailscale
brew install node 
brew install --cask ghostty


## AI
npm install -g @anthropic-ai/claude-code
npm i -g @openai/codex


### Mac mini defaults. 

Always On no sleep 
```
sudo pmset -a sleep 0 disksleep 0 displaysleep 0
sudo pmset -a hibernatemode 0 powernap 0
sudo pmset -a standby 0 autopoweroff 0
sudo pmset -a autorestart 1
```
Verify with `pmset -g | grep sleep`


```
cat > ~/Library/LaunchAgents/com.openclaw.caffeinate.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.openclaw.caffeinate</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/bin/caffeinate</string>
    <string>-s</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>
</dict>
</plist>
EOF

launchctl load ~/Library/LaunchAgents/com.openclaw.caffeinate.plist
pgrep caffeinate  # Should return a PID
```

### Setup User and set to auto login
Have a user that is created by you the human then create the following as an additional user give it admin rights.

```
sudo sysadminctl -addUser mini-ai -fullName "Full Name" -password "password" -admin
```

Verify: 
```
# List all users
dscl . -list /Users

# View a specific user's details
dscl . -read /Users/username
```

## Auto login 
Human: System Settings → Users & Groups → Login Options → Automatic login

#### Set the auto-login user
sudo defaults write /Library/Preferences/com.apple.loginwindow autoLoginUser "mini-ai"

##### Set password 
```
  # Using Python to encode the password
  sudo python3 -c "
  import struct
  key = [125, 137, 82, 35, 210, 188, 221, 234, 163, 185, 31]
  password = '$PASSWORD'
  # Pad to multiple of key length
  while len(password) % len(key):
      password += '\0'
  encoded = bytes([ord(c) ^ key[i % len(key)] for i, c in enumerate(password)])
  with open('/etc/kcpassword', 'wb') as f:
      f.write(encoded)
  " 
  
  # Set correct permissions
  sudo chmod 0600 /etc/kcpassword
sudo chown root:wheel /etc/kcpassword
```



