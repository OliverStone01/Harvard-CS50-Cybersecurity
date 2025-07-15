## Week 1 - Securing Data

Date: 14-07-2025

## Notes:

Last week when we looked at accounts, we mainly looked at securing accounts using a password. We mainly looked at how we can keep our password secure. But there is another party that needs to keep your details safe when you have an account with a username and password, and that is the `server/app` that is storing these details to check authentication.

In the simplest form, let's say that a server is storing user details as key-value pairs like such:
```
alice:apple
bob:banana
```
This is known as storing user details in `cleartext`. This is extremely insecure because if the database is `hacked` by an adversary, then the hacker would have access to all the passwords and usernames stored. The hacker could then use these details in a `credential stuffing attack` (trying the details on other sites).

As a security developer/analyst, it is your job to minimise the `fallout` if a database is hacked. To do this, we can use what's known as `hashing`.

-----

### Hashing

Hashing is where you take a password as input and use a mathematical algorithm to convert the password into a `hash`. A hash typically will look like a string of jumbled-up random letters and numbers but is typically a fixed length, no matter the length of the password. 

What we would then do is store the `hash` value of the password in the database. 
```

input -> [ hash function ] -> output

```
The mathematical version looks like this:
```

x -> [ f ] -> f(x)

```

Let's look at some basic hash outputs to help understand this further:
```
apple -> [ hash function ] -> ..ekWXa83dhiA
```
It is important to understand that hashes cannot be reversed.

Although it looks odd, it doesn't leak any data, which is really good as it will slow down the attacker and cost them more time and resources to get to the password. One way they can do that is via a `rainbow table`.

A `rainbow table` is a database filled with pre-hashed passwords, where hackers take the hash value from the data dump and compare it to the hash values in the database.

So how is a hash stored, and how does the site use the hash to authorise a user?

What happens is, when you sign up to a site, you enter a unique username and a password. The password is then sent to the server and is then passed through a hash function. The output of the hash function is then stored in the database and the cleartext is forgotten about. When you then come back to the site later on and want to sign into your account, you enter your username and password which is then sent to the server. The server runs the password you entered through the hash function and compares the hash to the hash stored in the database alongside the username you entered.

To do all this, you don't need to reinvent the algorithm. There are third-party libraries that you can use to take care of this for you.

Here is an example of stored usernames and hashes:
```
alice:..ekWXa83dhiA
bob:..ZS4zkCo/P7E
carol:..rj98gxDTYfm
charlie:..rj98gxDTYfm
```
The issue, as you can see, is that when you get two hashes of the same value, the hacker knows they only need to crack one of the passwords to gain access to all the passwords with the same hash. This is called a data leak.

To prevent this from happening, we can use what's called a `salt`. A salt is a value added to the start of the clear text password by the server before it is hashed. The salt is then stored at the start of the new hash value so that the server knows what salt is needed to hash the entered password when you try to log in.
```
// Using a 50 salt:

           ------
    50 -> |      |
cherry -> |      | -> 50kbbTM.xAinU
           ------

// Using a 49 salt:
           ------
    49 -> |      |
cherry -> |      | -> 493vg4DKt4MKg
           ------
```
What this does is create a lot more possibilities of hashes, creating even more cost in time and resources to the attacker as they now need to not only hash each possible value, but also the same with all types of salt. This makes it almost impossible for the hacker to crack the password. What it also does is because each user is assigned a different salt, many users (unless given the same salt) can enter the same password but look completely different in the database, like such:
```
alice:..ekWXa83dhiA
bob:..ZS4zkCo/P7E
carol:50kbbTM.xAinU
charlie:493vg4DKt4MKg
```
Here is what hashes generally look like today:
```
alice:$y$j9T$DIXfskUxbz6we5RbfdPCz0$zFd93]MuTyEJHrdIf.6bZ8Rbw40tHvybdOuLn.eD.53
```
This hash value is much larger, making it even more difficult for the hacker to crack the original password.

At the start of this hash function, you will see `$y$`; this is a cheat sheet that tells the system what hash function was used.

In the first example of hashing we did with the salt. The total amount of hashes you can have is:
```
18,446,744,073,709,551,616
```

With the last example we looked at, the total number of hashes you can have is:
```
115,792,089,237,316,195,423,570,985,008,687,907,853,269,984,665,640,564,039,457,584,007,913,129,639,936
```
Now, that would take the hacker many lifetimes to crack the password.

#### Forgotten password

If you ever use a site where you have forgotten your password and you use the `forgotten password` link. If the site displays your password to you, stop using this site. This is a clear sign that the server is storing your password in clear text, as that is the only way it would know what your password is, as hashing is irreversible.

"Verifiers SHALL store memorised secrets in a form that is resistant to offline attacks. Memorised secrets SHALL be salted and hashed using a suitable one-way key derivation function... Their purpose is to make each password guessing trial by an attacker who has obtained a password hash file expensive and therefore the cost of a guessing attack high or prohibitive."

When you refer to best practices, you are referring to documentation released by governments and the people who made the technology.

Here is a list of all the current common hash functions:
- SHA-224
- SHA-256
- SHA-384
- SHA-512
- SHA-512/224
- SHA-512/256
- SHA3-224
- SHA3-256
- SHA3-384
- SHA3-512
...

There are some other algorithms that can check the authenticity of a message:
- CMAC
- HMAC
- KMAC
...

All of these hashed values we have been looking at are called **One-way Hash Functions**.

These are functions written in code (such as Python) that take an input of arbitrary length (different lengths) and output a hash value of a fixed length.

-----

### Cryptography

Cryptography is all about the practice and study of securing our data, specifically when we want to transmit it.

#### Codes
This is mapping **code** words to the actual message. What would happen is you and the person you are sending the message to would have a book with a list of code words and their value, which they have been assigned to.

The sender would then use this book to convert the message they want to send using these code words so that the message is hidden from outsiders trying to read it.

When the receiver gets the message, they use the book to convert all the code words back to the original value (decoded), and then they are able to read the message sent.

The issue with this is that the users must have a book, and therefore the likelihood of this book being stolen or made a copy of is quite high, and that person can then intercept your messages.

**Encode** = To take plaintext and convert it into codetext.
**Decode** = To take codetext and conver it into plaintext.

There is an alternative to code books called **Ciphers**

-----

### Ciphers

Ciphers are algorithmic ways to encrypt messages, and instead of encrypting each word, we will encrypt each letter (each bit).

**Encipher**: Take a message in plaintext and convert it into cipher text. This is also known as **encrypt**.

**Decipher**: Take cipher text and convert it back to plaintext. This is also known as **decrypt**.

**Keys**: A key is what is used to encrypt and decrypt the message by the sender and the receiver.

**Secret-Key Cryptography**: This is where the two parties must keep the algorithm (key) they are using a secret; otherwise, people would be able to read the messages. This is also known as **symmetric-key encryption** because both parties are using the same key.

```
               - - - 
      key -> |       |
plaintext -> |       | -> cipher text
               - - -

// Simple example

      - - -
1 -> |     |
B -> |     | -> C
      - - -
```

This is also known as `Caesar cipher` or `rotational cipher` because the numbers are being rotated.

ROT13 is a well known cipher with a key of 13 so A becomes N.

It’s important not to use a key of 26 because otherwise A becomes A. There is a joke in cipher that goes "ROT26 is twice as secure at ROT13."

Here is a cipher, try to crack it:
```
OR FHER BG QEVAX LBHE BINYGVAR

// The key is 13 (ROT13)
BE SURE TO DRINK YOUR OVALTINE
```

To do this, we `decrypt` the message by taking the ciphertext and going through each possible letter, adding 1 to the key until the plaintext makes sense.

What is a good encryption now days?
- AES
- Triple DES

These are very common secret-key ciphers.

-----

### Public-key cryptography (asymmetric cryptography)

The issue with `secret-key cryptography` is that you are relying on both parties already knowing the secret key. But how would you begin communicating with someone new that doesn’t know the secret key?

The solution is `public-key cryptography`.

Some of the algorithms used are:
- Diffie-Hellman
- MQV
- RSA
...

With public-key encryption, everyone has two keys. One `private-key` that is meant to be kept secret and shouldn’t be shared with anyone, and a `public-key` which is shared with anyone you wish to communicate with.

The two keys have a mathematical connection which allows users to encrypt a message with one key and then decrypt it with the other key.

For example. If Alice wanted to send a message to Bob. What Alice will do is encrypt the message using Bob's public key. This will encrypt the message from outsiders being able to view the message, and the only person who is able to read the message is Bob when he decrypts the message with his private key.

RSA is a very common algorithm to create these keys. RSA uses this calculation to generate the keys:

// RSA formula
n = p x q

p = a really large prime number  
q = a really large prime number

c = m<sup>e</sup> mod n  
m = c<sup>d</sup> mod n


Using Diffie-Hellman, we can do `key exchange`. This is a way that two systems can use this algorithm to agree on a key that they can use as a secret key.

// Diffie-Hellman
A = g<sup>a</sup> mod p
B = g<sup>b</sup> mod p

s = B<sup>a</sup> mod p
s = A<sup>b</sup> mod p

-----

### Digital Signatures

Digital signatures are a way for you to be able to sign information and documents using your private key. Some of the algorithms used are:

- DSA
- ECDSA
- RSA
...

To sign something digitally, you take the message you want to send and pass it through a hash function, which is then taken and run through the `digital signature algorithm` with your private key.
```
message -> [ hash function ] -> hash

                - - - - - - - - - -
private key -> | digital signature |
       hash -> |     algorithm     | -> signature
                - - - - - - - - - -
```

The recipient then verifies your signature by using your public key.

```
               - - - - - - 
public key -> | decryption |
 signature -> |            | -> hash
               - - - - - -
```

If the reception gets the exact same hash as you, then it verifies that it is your signature.

-----

### Passkeys

There is one more application that solves the issues of needing passwords. It is called `passkeys`, also known as `web authentication`. This passkey relies on private and public keys.

Passkeys mean you will no longer need a username or password. Instead, you will need to create a passkey. What this means is your phone or computer will ask you for a `factor` such as a fingerprint or a scan of your face. The device will then generate a public key and a private key for that one app. 

When you then want to sign in later, you can use a cloud service which synchronises your passkeys across your devices. Then, using your fingerprint, you can authorise your self and sign in.

The website that you are signing into will then use the public key of your passkey for that site and be able to verify that it is you.

If you don't use cloud services and you lose your device. There is a chance you will lose access to your account.

-----

### Encryption in Transit

This is a fancy way of saying that our data should be encrypted when it's travelling from point `a` to point `b`. What we are trying to avoid is a `man-in-the-middle attack`.

### End-to-End Encryption

This allows you to be sure that the data is encrypted from beginning to end during the transit, no matter if there are other servers or systems linking the two together.

This can be seen with Apple's iMessage and WhatsApp. This means these companies can't see your messages even though they pass through their servers.

-----

### Deletion

Data is stored on hard drives, solid-state drives, and USB sticks on your computer. When you delete files on your computer, you are not actually deleting the file. Even when you empty the device's bin, all you are actually doing is telling the computer to forget the location of the data.

All the data is still actually on your computer and can now be overwritten, but a lot of the time it will only be partially overwritten, which means some of the file can still be located, making it insecure.

### Secure Deletion

This completely deletes data by changing the bits that were storing the data to all `0's` or `1's`, making the data completely unrecognisable.

### Full-Disk encryption

This is also known as `Encryption at rest`. It means encrypting the data on the device and keeping the data secure until the password is entered and the data is decrypted for use.

This doesn’t stop thieves from wiping your data and using the device, but it does prevent them from getting your data.

An evil side to full-disk encryption is ransomware, where attackers will get access to your system, lock it down with encryption, and request payment to decrypt your data.


### Quantum computing

All computers use bits to store and make up data (`0's and 1's`). In quantum computing, a bit can be thought of differently using quantum physics. A bit can become a quantum bit, also known as a `qubit`. This means that the qubit can represent a 1 and a 0 at the same time. This means one qubit can be in two states at one time.

- 2 qubits = 4 states at once.
- 3 qubits = 8 states at once.
- 32 qubits = 4 billion states at once.

What this means is your computing power is ridiculously improved, which makes it dangerous to our current security.
