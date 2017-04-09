# google-authenticator-desktop

Follow the instructions here to generate Google 2 step authentication OTP without using your mobile.

## Contents
1)  How does it works? motivation (and de-motivation)
2)  Generating OTP token from your computer

### How does it work?

### Generating OTP token from your computer

1.  acquire your secret key
  * Login to Google on your computer and enter your account settings > Signing in to Google > 2-Step Verification (exact directions may change over time...)
  * Configure authenticator app to get the QR code and scan it (easiest way would probably be with an app on your phone...)
  * the coded string should be something of the form: otpauth://totp/[YOUR_USER_NAME]?secret=[YOUR_GOOGLE_AUTH_SECRET]
  Save the secret for later
  
2.  install OAuth toolkit: (e.g., using Homebrew)
`brew install oath-toolkit`
3.  calculating the current OTP based on your secret
`oathtool --totp -b #YOUR_GOOGLE_AUTH_SECRET`

### Now, let's automate it... (OS X only. if anyone has instructions for other OS, you are welcome to contribute... :) )

create a command to copy the current OTP and print to stdout:

* create a file in /usr/local/bin, named <your-alias> with the following content (don't forget to put your actual secret!!):
```
echo $(oathtool --totp -b #YOUR_GOOGLE_AUTH_SECRET#) | pbcopy;
echo $(pbpaste);
echo "you have $(((30 - ($(date +%s)) % 30))) seconds left";
```

(1st row copies to clipboard, 2nd+3rd rows print to output)

* make the file executable by running `chmod 755 <filename>`

assuming you have proper permissions, this should work:


```
echo 'echo $(oathtool --totp -b #YOUR_GOOGLE_AUTH_SECRET#) | pbcopy; echo $(pbpaste); echo "you have $(((30 - ($(date +%s)) % 30))) seconds left";' > /usr/local/bin/gen-google-otp
chmod 755 /usr/local/bin/gen-google-otp
```

...and create a command named "gen-google-otp". don't forget to replace "#YOUR_GOOGLE_AUTH_SECRET#" with your actual secret!
