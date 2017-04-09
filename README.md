# google-authenticator-desktop

Follow the instructions here to generate Google 2 step authentication OTP without using your mobile.

## Contents
1)  How does it work? motivation (and de-motivation)
2)  Generating OTP token from your computer

### How does it work?

OTP-based 2-steps authentication - the 2nd step:
A password created by the user can be cracked in many ways... so what's the point in having another password that can be cracked in the same manner?
The answer is: create a password without ever letting the user knowing it!
How do you do it? never ask the user for the password, instead ask for a "proof" the user has the password! but then... isn't having the proof equivalent to having the password? no, because each "proof" is time-limited, and only valid for a small time-frame (30 seconds).
to wrap it up: code the secret as a QR code to allow transferring the secret to a secondary device easily and without explicitly exposing the secret to the user, and create a utility that calculates a temporary "proof", given the secret and the current time [important to note: it's a static and deterministic calculation performed offline on both sides, considering they both have synchronized clocks and they both know the secret].

now... while the "secret" of the 1st step is a password chosen by the user and has all the associated vulnerabilities (and the user is required to ^explicitly^ expose it periodically), the secret of the 2nd step is only stored on the secondary device, so it is exposed only for the initial setup of the process.

of course, there are ways to bypass that as well, but that's way safer. are you keeping the QR code or the secret somewhere in your inbox? that kind of nullifies the "2 factor" idea... if you still don't care, read on :)

### Generating OTP token from your computer

#### Doing it manually

1.  acquire your secret key
  * Login to Google on your computer and enter your account settings > Signing in to Google > 2-Step Verification (exact directions may change over time...)
  * Configure authenticator app to get the QR code and scan it (easiest way would probably be with an app on your phone...)
  * the coded string should be something of the form: otpauth://totp/[YOUR_USER_NAME]?secret=[YOUR_GOOGLE_AUTH_SECRET]
  Save the secret for later
  
2.  install OAuth toolkit: (e.g., using Homebrew)
`brew install oath-toolkit`
3.  calculating the current OTP based on your secret
`oathtool --totp -b #YOUR_GOOGLE_AUTH_SECRET`

#### Now, let's automate it... (OS X only. if anyone has instructions for other OS, you are welcome to contribute... :) )

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

...and should create a command named "gen-google-otp". don't forget to replace "#YOUR_GOOGLE_AUTH_SECRET#" with your actual secret!

### Troubleshooting

* make sure that whatever device you are using to calculate the OTP has a synchronized clock. it's best to let your OS autosynchronize the time.
* having Homebrew installed is a prerequisite https://brew.sh/. otherwise you may find a different way to install oauth tool.
* you may have permission issues with creating the command. stackoverflow is your friend.
