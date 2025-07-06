## Week 0 - Introduction to cybersecurity

Date: 06-07-2025

## Notes:

### Securing accounts

In the physical world, security can be seen everywhere. Your home will have windows and doors which will be locked to keep your possessions secure. To gain access to your home, you have a key.

The key issue is, if someone else gets that key, they can also access your home. 

In the digital world, we think of the security of our accounts as `authentication`.

`Authentication` is the process of digitally proving who you are.

Although just because you are able to prove who you are, doesn't mean you should be able to access everything a site has. To prevent you from being able to go where you like, we use `authorisation`.

`Authorisation` is whether you should have access to something once you have proven who you are.

When it comes to our digital accounts, we don't use physical keys to prove who we are; instead, we use `usernames`, which are okay to be public, and we also use `passwords`, which you must keep private.

Your `password` allows you to authenticate yourself as you are the only person who should have this information.

It is important that you don't use the same password across different sites because if one site has a password leak, the attackers can use that password on other sites and get access to those accounts. 

It's also important you have a good password. A weak password would be a word that's inside a dictionary. This is because there is an attack known as a `dictionary attack`.

A dictionary attack is where a hacker will open a file full of words (a dictionary) and go through each word attempting to guess your password.

If you think that an easy way around this would be to add numbers, symbols, and capital letters. The attackers can do what is known as a `brute-force attack`.

A `brute-force attack` is where an attacker can run software to try every single password combination to get access to your account.

How do we defend ourselves against these styles of attacks?

Passwords are one method, but the bar is set so low for what is required for a strong password. For example, some phone manufacturers only require you to enter a `4-digit` PIN for unlocking your device.

4-digit PIN = 10,000 possible passwords.
(`0000` smallest + `9999` largest = 10,000)
10 to the power of 4

Using software, this system would not take very long to break. But how do we measure how long it takes? 

`crack` = To break into or brute-force into.

We are going to write some Python code that prints out each possibility of passwords to measure the time taken.

The first thing we do is import the digits library so we don't need to manually type out 0 to 9.
```
from sting import digits
```

Here is the full code (although it is not the greatest code) for running each possibility in milliseconds:
```
from string import digits

for i in digits:
    for j in digits:
        for k in digits:
            for l in digits:
                print(i, j, k, l)
```

How can we improve the security then from a 4-digit code? How about we try four letters instead?
```
26 x 26 x 26 x 26 = 456,976 possibilites
```

How about we include lower case and upper case characters:
```
52 x 52 x 52 x 52 = 7,311,616 possibilities
```
```
from string import ascii_letters

for i in ascii_letters:
    for j in ascii_letters:
        for k in ascii_letters:
            for l in ascii_letters:
                print(i, j, k, l)
```

This time it's taking a lot longer than last time, but in total still only took a couple of seconds.

So how can we improve this further? How about we do a 4-digit combination of numbers, letters, and punctuation:
- 26 lowercase letters (a-z)
- 26 uppercase letters (A-Z)
- 10 decimal digits (0-9)
- 32 punctuation symbols
```
94 x 94 x 94 x 94 = 78,074,896 possibilities 
```

Now this will take ten times longer than before. This will take a couple of minutes to complete, which is definitely an improvement on before.

But thankfully, nowadays, the average minimum password length required is around eight characters.
```
94 x 94 x 94 x 94 x 94 x 94 x 94 x 94 = 6,095,689,385,410,816 possibilities
```
Now that is a lot more possibilities, and as long as we don't choose a simple password like `00000000`, then the password will take the hacker a really long time to crack.

The only issue is, the more difficult the password, the harder it is for the user to remember. 

What can we do to protect ourselves against brute-force attacks? In the US, there is a company called:

`NIST` = National Institute of Standards and Technology

NIST issues recommendations to consumers and companies on how to stay protected like securing your accounts.

Here is a quote for their recommendations about passwords:
"Memorised secrets SHALL be at least 8 characters in length..."

"Verifiers SHOULD permit subscriber-chosen memorised secrets at least 64 characters in length. All printing ASCII characters as well as the space characters SHOULD be acceptable in memorised secrets. Unicode characters SHOULD be accepted as well".

"... verifiers SHALL compare the prospective secrets against a list that contains values known to be commonly used, expected, or compromised...

- Passwords obtained from previous breach corpora.
- Dictionary words
- Repetitive or sequential characters (e.g. 'aaaaaa', '1234abcd').
- Context-specific words, such as the name of the service, the username, and derivatives thereof."

"Memorised secret verifiers shall NOT permit the subscriber to store a "hint" that is accessible to an unauthenticated claimant. Verifiers SHALL NOT prompt subscribers to use specific types of information (e.g., 'What was the name of your first pet?') when choosing memorised secrets."

"Verifiers SHOULD NOT require memorised secrets to be changed arbitrarily (e.g., periodically)"

"Verifiers SHALL implement a rate-limiting mechanism that effectively limits the number of failed authentication attempts that can be made on the subscriber’s account..."

Apple and other phone manufacturers implemented a feature to lock the user's device for a set period of time if the user failed to input their password more than ten times.

This is because, the manufacturer believes, if you failed this many times, you must be an attacker and not the owner of the device. The timer slows the attacker down, increasing the cost to the attacker.

This takes the example we looked at earlier of trying 10,000 codes in milliseconds to hours if not days depending on the timers and only allowing the hacker to try ten codes at a time.

-----
#### 2FA

`2FA` = Two-factor authentication.

Also known as Multi-Factor Authentication. This increases the security of your device by increasing the number of ways you need to authenticate yourself. For example, a password being the first factor, and a text to your phone being the second factor. 

There are three types of factors:
- Knowledge (something you know (password))
- Possession (something you have (text to your phone))
- Inherence (something you are(fingerprint))

`OTP` = One-Time Password.

An example of an OTP is a text sent to your device as a second authentication.

A better version of the OTP being sent over text is a OTP through an app. This is because hackers found a way to SIM-swap your phone number, sending the OTP to the hackers device and allowing them to gain access to your account.

-----

#### Keylogging

If you downloaded a piece of software that you thought was safe but actually turned out to be some malware, the hacker could now have embedded a keylogger onto your device, which is now recording all the keystrokes you input on the keyboard, collecting data such as your passwords, bank details, and much more private data.

------

#### Credential Stuffing

This is taking usernames and passwords from a data breach and trying them on a diffrent website to see if the user has used the same credentials across different sites.

-----

#### Social Engineering

Social engineering is the act of convincing someone to do something they shouldn't do. In the class, David gave an example by asking people in the class to write down one of their passwords on a piece of paper in front of them. This is a social engineering attack as David is someone who you would believe you could trust and then has asked you to do something you shouldn’t do but because you trust him, you did it.

------

#### Phishing

Phishing is a technical version of social engineering. You are using (usually email) to convince someone to give you secret information.

For example, you may receive an email saying you have won something and all you need to do is enter your card details to pay for shipping. This attacker would of just phished your banking information (if you gave them the information).

-----

#### Machine in the middle attacks

When you request information on the internet, or submit information. Your data is sent through many devices (like routers and servers) to get to the final server. If one of those devices is malicious, they could be stealing your data or information as it's being passed back and forth.

-----

### Some solutions

Although we have seen all of these attacks, having a 64-character password and having it all random characters would be super secure. It is not user-friendly and will not be used for very long. So what are some ways we can combat this...

`SSO` = Single Sign-On

Single sign-on allows you to sign up/log in to a website using an account you have with another website. This is commonly seen with large companies like with Google, Apple, and Facebook. 

This means you don’t need to have another account, with another password, meaning there is less friction when it comes to security practices.

What is happening when you use these is you are taken to Google, for example, where you must sign in with your username and password. Once you are signed in, Google then returns to the website with the username and a confirmation to say "Yes, this person is verified. You can trust that this is their username" and then you are signed in. 

-----

#### Password manager

A password manager is a piece of software that will help you store your passwords for you, but also come up with secure passwords for you. This prevents you from using the same password everywhere. 

The password manager will also allow you to sign in with a press of a button, as it will fill in your data for you automatically. It will also only do this on the real sites, as it remembers the URL where the account was created and used.

The catch is that you must remember the password that protects your password manager. This is the same as putting all your eggs in one basket.

Some of the main ones are:
- Apple iCloud Keychain
- Google Password Manager
- Microsoft Credential Manager

-----

#### Passkeys

Passkeys are an alternative that is coming into play now, where your device will do all this for you. Your Mac or your phone will authenticate for you.
