## Week 1 - Securing Data

Date: 14-07-2025

## Notes:

Last week when we looked at accounts, we mainly looked at securing accounts using a password. We mainly looked at how we can keep our password secure. But there is another party that needs to keep your details safe when you have an account with a username and password and that is the `server/app` that is storing these details to check authentication.

In the simplist form, let's say that a server is storing users details as key value pairs like such:
```
alice:apple
bob:banana
```
This is known as storing users details in `cleartext`. This is extemely insecure becuase if the database is `hacked` by an adversary, then the hacker would have access to all the passwords and usernames stored. The hacker could then use these details in a `credential stuffing attack` (Trying the details on other sites).

As a security devloper/analys, it is your job to minimise the `fallout` if a database is hacked. To do this we can you what's known as `hashing`.

-----

### Hashing

Hashing is where you take a password as an input and use a mathmatical algorithm to convert the password into a `hash`. A hash typically will look like a sting of jumbled up random letters and numbers but are typically a fixed length no matter the length of the password. 

What we would then do is store the `hash` value of the password in the database. 
```

input -> [ hash function ] -> output

```
The mathmatical version lookes like this:
```

x -> [ f ] -> f(x)

```

Let's look at some basic hash outputs to help understand this further:
```
apple -> [ hash function ] -> ..ekWXa83dhiA
```
It is important to understand that hashes cannot be revered.

Although it looks odd, it doesn't leak any data which is really good as it will slow down the attacker and cost them more in time and resources to get to the password. One way they can do that is via a `rainbow table`.

A `rainbow table` is a database filled with pre-hashed passwords where hackers take the hash value from the data dump and compare it to the hash values in the database.

So how is a hash stored and how does the site use the hash to authorise a user?

What happens is, when you sign up to a site, you enter a unique username and a password. The password is then sent to the server and is then passed through a hash function. The output of the hash function is then stored in the database and the cleartext is forgoten about. When you then come back to the site later on and want to sign into your account, you enter your username and password which is then sent to the server. The servers runs the password you entered through the hash function and compares the hash to the hash stored in the database along side the username you entered.

To do all this, you don't need to reinvent the algorithm. There are third party librabrys that you can use to take care of this for you.

Here is an example of stored usernames and hashes:
```
alice:..ekWXa83dhiA
bob:..ZS4zkCo/P7E
carol:..rj98gxDTYfm
charlie:..rj98gxDTYfm
```
The issue as you can see is that when you get 2 hashes of the same value, the hacker knows they only need to crack one of the passwords to gain access to all the passwords with the same hash. This is called a data leak.

To prevent this from happening, we can use what's called a `salt`. A salt is a value added to the start of the clear text password by the server before it is hashed. The salt is then stored at the start of the new hash value so that the server knows what salt is needed to hash the entered password when you try login.
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
What this does is create a lot more possibilites of hashes creating even more cost in time and resources to the attacker as they now need to not only hash each possible value, but now need to the same with all types of salt. This makes it almost impossible for the hacker to crack the password. What it also does is becuase each user is assigned a diffrent salt many users (unless given the same salt) can enter the same password but look completly diffrent in the database like such:
```
alice:..ekWXa83dhiA
bob:..ZS4zkCo/P7E
carol:50kbbTM.xAinU
charlie:493vg4DKt4MKg
```
Here is what hashes generaly look like today:
```
alice:$y$j9T$DIXfskUxbz6we5RbfdPCz0$zFd93]MuTyEJHrdIf.6bZ8Rbw40tHvybdOuLn.eD.53
```
This hash value is much larger making it even more difficult for the hacker to crack the original password.

At the start of this hash function you will see `$y$`, this is a cheet sheet that tells the system what hash function was used.

In the first example of hashing we did with the salt. The total amount of hashes you can have is:
```
18,446,744,073,709,551,616
```

With the last example we looked at, the total amount of hashes you can have is:
```
115,792,089,237,316,195,423,570,985,008,687,907,853,269,984,665,640,564,039,457,584,007,913,129,639,936
```
Now that would take the hacker many lifetimes to crack the password.

#### Forgotten password

If you ever use a site where you have forgotten your password and you use the `forgotten password` link. If the site displays your password to you, stop using this site. This is a clear sign that the server is storing your password in clear text as that is the only way it would know what your password is as hashing is irreversable.

"Verifiers SHALL store memorized secrets in a form that is resistant to offline attacks. Memorized secrets SHALL be salted and hashed using a suitable one-way key derivation function... Their purpose is to make each password guessing trial by an attacker who has obtained a password hash file expensive and therefore the cost of a guessing attack high or prohibitive."

When you refer to best practices, you are refering to documentation released by goverments and the people who made the technology.

Here is a list of all the current comman hash functions:
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

There are some other algorithms that can check the authnticity of a message:
- CMAC
- HMAC
- KMAC
...

All of these hashed we have been looking at are called **One-way Hash Functions**.

These are functions written in code (such as python) that take an input of arbirtary length (diffrent lengths) and output a hash value of a fixed length.

-----

### Cryptography

cryptography is all about the prctice and study of securing our data specifically when we we want to transmit the data.

#### Codes
This is mapping **code** words to the actual message. What would happen is you and the person you are sending the message too would have a book with a list of code words and their value they have been assigned to.

The sender would then use this book to convert the message they want to send using these code words so that the message is hidden from outsiders trying to read the message.

When the receiver get's the message, they use the book to conver all the code words back to the original value (decoded) and then they are able to read the message sent.

The issue with this is that the users must have a book and therefor the likely hood of this book being stollen or made a copy of is quite high and that person can then intersept your messages.

**Encode** = To take plaintext and convert it into codetext.
**Decode** = To take codetext and conver it into plaintext.

There is an alternative to code books called **Ciphers**

-----

### Ciphers

Ciphers are algorithmic ways to encrypt messages and instead of encrypting each word, we will encrypt each letter (each bit). 



















