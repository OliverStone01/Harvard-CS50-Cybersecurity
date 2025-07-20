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

The third-parties that are signing these certificates are called `Certificate Authority (CA)`. They are a collection of companies that sign the certificates for the browsers to use.

When you visit a site, the browser first downloads the certificate. The browser then takes the certificate with the specific fields and runs it thorugh a specific hash function. Then your browser then takes the public key of the `CA` that signed that certificate and it takes the signature from the certificate and hashes them through another specific hash function. If the hashes match, you can trust the connection with this website.

Does TLS and HTTPS keep you secure?

Mathmatically yes, but humans are the potential weekness. There is an attack called SSL stripping. This attack fools the users to thing that they have a secure connection to the site they are tryign to use.

What happens is that when we search in browsers now days, we are no longer (out of time saving habits) typing out the entire URL and intead are only typing `example.com`. This means that your browser has to figure out which site you are trying to use and direct you to that site.

What an attacker can do is intercept this and direct you to a site that looks exactly like the site you are trying to access but is insecure and they have access to all the information (log in details, bank details) that you use on the site.

You browser packet will look like this if you search `example.com` and not the entire URL:
```
GET / HTTP/3
Host example.com
```

Then the server may return the following:
```
HTTP/3 307
Location: https:examp1e.com/
```

Code 307 = Redirect to the location URL.

What can happer is the attacker can respond with this packet redirecting you to their site. The cleaver part about this is that because the new location URL is HTTPS, you believe you are at a secure site, but if you take a look at the name of the URL, it is actually `examp'1'e.com`.

How do we mitigate this attack? As the user, you could get into the habit of writing out the entire url (bit of a long approach) but it is the best meathod. 

If you are the designer of the website, then you can use a protocol called `HSTS`.

`HSTS` = Hyper-text strict-transport security.

What this does is provide hints to the browser to always use HTTPS for example. It adds an additional header telling the browser that you want to be stricked with the data transportation security.

```
strict-transport-security: max-age=31536000
```

What this does is tell the browser to keep this level of security for a year.

We can also add this security to subdomains as well by doing: 
```
strict-transport-security: max-age=31536000; includeSubDomains
```

This is great becuase after this package has been received by the browser, it will continue to use this for a year any time the browser goes to the site and it will not allow the browser to go to HTTP.

You can also add `preload` like such:
```
strict-transport-security: max-age=31536000; includeSubDomains; preload
```

What this does is tell the browsers to prelaod your website into the browsers install package so that you don't need to worry about the first time the user visits your website (before they get the package that tells them not to use HTTP because it will already be in the browsers source code.

### VPR

`VPN` = Virtual private network.

This encrypts all your data being sent from point a to point b. To do this, it creates a tunnel from a to b that only you and the receiver have access to the tunnel and its data. 

If you work at a company, chances are they will set you up with a VPN to connect to the network at that company. what this means is all the data sent between the two is fully encrypted.

### SSH

`SSH` = Secure Shell

This is mostly used by developers and programmers. It is great for connecting to other devices on the network you are connect to via text based commands instead of a `GUI`.

Here is some example of propts and how to connect to another device.

`$` Type command here

Show date:
```
$ date
Thu Jan 1 12:00:00 AM EST 1970
```

Connect to another device:
```
$ ssh stanford.edu
```

This means you can control server from server and it is fully encrypted.

-----

### Port

On the outside of these packets (envelope) is a port number which tells the world what service this packet is for. For example, if you were to pull up a website, you would get a port number of `80`. If the webserver responds and tells the browser to use HTTPS instead, then the browser will need to use port `443`. 

Common port numbers:
HTTP = 80
HTTPS = 443
SSH = 22
...

What computers do is listen for packets on these ports. What you might be thinking is well if the attackers know what the port number is, they might try to access your server through the port. What you might think to do is change the port to a diffrent number between 0 and 65000. But the issue is, attackers are able to do what's called `port scanning` where they can scan all the ports available.

This is sometimes used by `penetration testers` to check your network for cracks and help you by identifying these issues so you can repair them.

Someone else who might use this are `ethical hackers`. These are people who hack websites and other systems to get them repaired and keep them safe from malicous hackers.

In a company, you may be put into two teams to test the network, these are known as `red team (hackers` and `blue team (defenders)`.

How do we keep these attackers out? We can use `firewalls`. Firewalls is software that protects the data inside of the network from getting out and keeps outside data from getting in. Admins can edit what form of data or authentication that is allowed to pass through.

The firewalls work by blocking packets based on IP addresses, port numbers, and by deep packet inspection. What this means is if you wanted to block specific sites on you home network, you can configure the firewall to block packets from the ip address of that site. 

You can also block the enitre internet if you wanted but then unblock the port 22 for SSH so that you can still remote into devices.

A deep packet inspection means that some firewalls can open up packets and see whats inside of them. This then allows you to block other headers and values.

To implement this type of firewall, we need to use a proxy. A proxy is a server or a piece of software on your network that passes data from a to b checking the packets and choosing whether to send the packets to b or not.
```
a <---> Proxy <---> b
```

When working for a company or school where you are provided a laptop, chances are they are tracking and monitoring everything you are doing on that laptop by using a proxy.

If you have a work email address, what the company could be doing is anytime you get sent a link (URL) to a website and you click on it. It sends you to a company site followed by a query with the original URL. This was a company can track the sites you have visited and can also prevent phishing attacks.

-----

### Malware

Malware is malicious software that is written by someone. Malware comes in many diffrent forms.

#### Virus
A virus is malware that attaches it self to a host. To get a virus, you would have to download a file and run it on your device for the virus to take effect. It can also be on a email link that you might click on. A virus relies on human error.

#### Worm
A worm is similar to a virus as in it can do anything to your computer. The major diffrence is that a worm doesn't need to be installed by a human or rely on human error at all. A worm, once installed on one device in the network, can use port scanning to search for other unprotected devices on the network and spread to them. 

With worms, people create `botnets`. A botnet is a network of computers under one control. What attackers will do is spread malware that doesn't do anything to your computer but sits listening for commands. Once the attacker has spread the malware to thousands or as many devices as they wish. They can use all those devices at once to form a much larger attack.

One attack someone might use a botnet for is a `distributed denial-of-service attack (DDoS)`. A `Dos` on it's own is a sigular device trying to deny service by providing lots of requests to a site so that it becomes unavailable to others by overwelming the site.

The issue is, with one device, you probably don't have enough resources to overwelm the site. Instead, they use `DDoS` by bringing all those computers in the botnet to attack at the same time. Now, you might have enough resources to block the site.

A `DoS` is much easier to block as a company becuase the attack will be coming from one IP address. But a `DDoS` is much harder as it might be thousands of IP addresses. 

### Antivirus
Antivirus is software downloaded on your computer that can detect viruses and other malware on the device. What it can do is then set to delete/remove the virus once you restart your device. The issues here are: One, the antivirus needs to have knowledge of the virus so that it knows what to look for. Second, It will not stop the malware getting on the device in most cases. Instead it will delete it once it's found. Meaning if you have already run the malware, it will omly delete it from the next time you restart your device.

Because antivirus software must know the malware to protect against it. Your device is still vulnerable to `zero-day attacks` which are attacks that the manufacturer or antivirus companies have no knowledge about.

