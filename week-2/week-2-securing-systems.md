## Week 2 - Securing Systems

Date: 17-07-2025

## Notes:

This week we are looking at securing systems, perticularly, those that are network connected or inter-networed. Encryption that we looked at last week will be the building block we are going to solve alot of the concerns around security.

For example, WIFI. When connecting to a WIFI, you first need to select the WIFI you want to connect to. You will notice there are two types of networks to choose from.
- Secure networks - Password protected.
- Unsecure networks - Public WIFI.

With secure networks, the little passlock indicates that you WIFI connection is encrypted.

What that means is any of the internet packets storing data are encrypted as they are sent to the internet.

The technology that is doing thsi is called:
#### WIFI Protected Access (WPA)

What this does is encrypt the packets from your device to the device you are trying to sent the data to.

Why do we want our WIFI connection to be encrypted?

When connecting to the internet and using websites, there is a protocol called HTTP (Hyper-text markup language). This is what displays text, images, and much more to your browser.

The issue with using http is it is liable to evesdroping (man-in-the-middle attack). This is because by definition, HTTP is not encrypted. This is bad becuase not only are you vulnerable to someone listening to the data you are sending and receiving, but they can also manipulate the data you are sending and receiving. 

A common attack looks something like this:
```
<!DOCTYPE html>

<html>
  ...
  <body>
    <script src="ad.js"></script>
    ...
  </body>
</html>
```

What this attack does is display adverts to every site that the user visits. This is a very simple attack but it shows that attackers can do anything if they wanted to, including malisious things.

### Packet sniffing

Packet sniffing is reading the data inside of a packet as it passes by. This is possible if the packet is not encrypted. 

Here is an example of data inside of an evelope of a browser requesting a website from a server.
```
GET /search?q=cats HTTP/3
Host: example.com
```
From this, the server will then hopefully send you back a lot of search results for "cats" in this case. When this packet is sniffed by an attacker, they can see what you are searching for.

Now the example above is not that bad, but what about if you were checking out on a website and you needed to provide your card number like this:
```
POST /checkout HTTP/3
Host: example.com

number = 424242424242424242
```
Well the now the attacker can see your card number and any other data that gets posted with this packet.

### Cookies

When you visit a website for the first time, you will be asked about the site collecting cookies. What this does, especially if you need to log in, is remeber who you are so that what you are navigating around the site or adding things to your basket, it can remember who you are and what is addressed to you.
```
HTTP/3 200
Set-Cookie: session=1234abcd
```
You can think of this like having your hand stamped and preventing you from needing to enter your password again and again.

This is an example of displaying to the website your cookie so that it known who you are so you dont need to login:
```
GET /HTTP/3
Cookie = session=1234abcd
```

If you are using HTTP, then again this data is inside of the packet that the user can sniff and see the contents of. This makes you vulnerable to `session hijacking`. This means that the attacker can set their cookies to look like yours and get access to your account without the need for your username or password.

-----

### HTTPS

HTTPS is the secure encrypted version of HTTP. It ensures all packets and data is encrypted as it is sent over the internet.

To do this, you need to use `TLS`. TLS relise on public and private key certificates. A browser will now have what's called a `digital certificate`. This is a public-key that has been signed by some thrid-party. The certificates are of type `X.509`. This is just a standard format in which the certificates live.

On this certificate are things like, name of the website, how long the certificate is valid for, and the public key.

The third-parties that are signing these certificates are called `Certificate Authority (CA)`. 














