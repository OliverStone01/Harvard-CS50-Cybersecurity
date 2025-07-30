## CS50 Cybersecurity - week 4 - Preserving Privacy

Today, we are going to look at some of the technologies that we use everyday. The first we are going to look at is `web browsing history`.

### Web Browing History

Wheather you are on a laptop, desktop, or mobile. Your web brower will keep track of all the sites you have recently visisted. This is really useful for if you forget what site you was using for a specific task. 

Your web browsing history is also used for things like autocomplete. When you start typing a URL or even a keyword from that page, the web browser can suggest that page from your history.

This is also a concern for privacy, becuase if anyone has access to your device, they can see what sites you have been looking at. 

To get rid of this threat, we need to sanitise our history. One way is to clear the history within the browser. This will clear all of your search history, but if you incude clearing all other data, it will also clear your cookies and your log in data meaning you will have to sign to all your accounts again. 

Another issue with only clearing your history is that you are not removing your trace. The web server collects logs of everyhting you do on a site and what sites you have visited. This means that data engineers can look at these logs if they needed to know what happened at a certain times or if something went wrong on the site. These logs are also used for marketing becuase the buisness can see where most of the users are coming from (device/browser wise and geolocation) and can use this information to target certain demographics.

Here is some of the data that these logs are tracking on a web server:
- $remote_addr
- $time_local
- $request
- $http_referer
- $http_user_agent

You have much less control on deleting and storing this information.

Let's look at what we can control. Let's take a look at `HTTP headers`. If you search for cats on Google, you will be visiting a URL that might look like this:
```
https://www.google.com/search?q=cats
```

When you visit this site, inside of your headers you will have a header that looks like this:
```
referer: https://www.google.com/search?q=cats
```

This header records where you came from before getting to the page you are on now. This is useful for analytics as they can see what sites people are coming from to get to their site. 

Fun fact: `referer` is not spelt correctly.

What some browsers do is add this tag to the headers:
```
<meta name="referrer" content="origin">
```

This tag means that the browser can only see the origin of the URL not the entire URL. The above example would look like this:
```
referer: https://www.google.com/
```

If you don't want any of this data being sent, you can set the following header:
```
<meta name="referrer" content="none">
```

If you have a little more control and you are able to edit the headers on the web server, then you can set the header `referrer-policy: origin`.

Or you can set this to `referrer-policy: no-referrer` to not track at all.

-----

### Fingerprinting

There is some other information that your browser is collecting. It is using this information to fingerprint you. What this means is to take a load of characteristics and use it to profile you.

The browser wouldn't know that it is exactly you, but they can infer with high probability that a user is the same. They can do this through many things, such as your IP address. The reason they have access to your IP address is becuase your IP address is set to the return address for the data to be sent back to.

This wont exactly identify you becuase if you are in an organisation or a company, everyone will most likly be the same as it is displayed by the same router. 

But the browser is sending other information like `User-Agent`. This is a unique string that tells the webserver what `browser` you are using and the `version`. It also tells you what `operating system` you are using and also the `version`.
```
Mozilla/5.0 (Linux; {Android Version}; {Build Tag etc.})
AppleWebKit/{WebKit Rev} (KHTML, like Gecko)
Chrome/{Chrome Rev} Mobile Safari/{Webkit Rev}
```

The web server can also interigate a browser for information to find out more information. For example, the server can ask for the screen resolution. Not just for displaying options, but it's also more information on you.

They will also look at the fonts you are using. The timezone that you are using. 

All of this information can be used for targated ads and data. 

-----

### Session Cookies

Cookies are a peices of information that a server puts on your computer to help remember who you are. A session cookie are used by servers to maintain state between the server and the browser. This allows us to maintain shopping carts when we come back to a site. 

When you load a site, the header might look like this:
```
HTTP/3 200
Set-Cookie: session=1234abcd
```

Session Cookies have a much shorter TTL (time to live) than other cookies. Once you have your cookie and you request another page on that site, your request might look something like this:
```
GET / HTTP/3
Cookie: Session=1234abcd
```

-----

### Tracking Cookies

Tracking cookies are the cookies that you read about being bad. What they are supposed to be used for is to remeber who you are, a bit like what we discussed with normal cookiess. The issue with these cookies is that they don't just track your activity on one site, they track your activity across all sites you visit. This is because they want to find what to advertise to you, analytics about you, and also for debugging purposes to see where things are going wrong.

Here is an example of this type of cookie that Google would use for analytics:
```
Set-Cookie: _ga=GA1.2.0123456789.0; max-age=63072000
```

This cookie would link to Google analytics which is a site that web developers can use to see more information about the users on the site.

The cookie age here is displayed in seconds. The age in this example is equivelent to `two year`.

-----

### Tracking Parameters

If you are in the habit of clearing your history, and deleting your cookies, then that's a good start. Unfortunatly, the server can still track you using `tracking parameters`. These tracking paramaters are sent in HTML get requests and can be seen in the URL like this:
```
https://example.com/ad_engagement?click_id=YmVhODI1MmZmNGU4&campaign_id=23
```

Thankfully, most browsers now days will see this request and will remove it for you as they understand that it is only there to track users.

-----

### Third-party Cookies

Third-party cookies are like the example we looked at above. If you were to access a site and they are using Google analytics and their own site cookies, then the Google analytics cookies would be classed as `thrid-party cookies`. 

What most browsers offer now days is the option to disable third-party cookies.

-----

### Private browsing

When you are using private browser tabs, you are essentially starting fresh with the web server. This is because when you close the private browsing tab, none of the cookies or paramater trackers are saved and everything is removed.

This is great when you want to search without the cookies following you after you have complete with your search, but this is not great for staying signed into your account. There is also still the chance that the server can fingerprint you by constantly seeing the same data and information from the same place (again, this does not mean they know exactly who you are).

-----

### Supercookies

These are cookies that are injected by companies like your internet provider or the company you are working for. These are injected into your HTTP requests. 

It's been known for mobile phone carriers to plant a cookie into your browser when searching sites. The key to remeber is always use encrypted HTTP and you will be protected. 

-----

### DNS

`DNS` = Domain Name System.

All computers have an IP address to which identidies and allows servers to send data to those IP addresses. Think of these like home addresses where mail is sent.

To prevent the need of us humans to remeber all these numbers to access out sites, we have DNS which is a server which stores a dictionary of all the IP addresses and the name of the website that they are linked to.

This means, instead of needing to remeber and search for Googles IP address each time you want to go to Google something, instead we can search for `Google.com` and the DNS server will find what the IP address is of `Google.com` and return this data back to you.

DNS servers are provided to you through your router that is provided to you from your internet service provider. Your internet provider will have a much larger DNS on their server than what is on your router.

DNS servers store the site that you search for the most so that it doesnt need to work hard to get the information you need. When you visit a new site for the first time, chances are your DNS doesn't know the IP address of the site you are looking for. This is when it will reach out to the other DNS server for the information it needs.

The data from DNS is unencrypted, which means that when your DNS is asking what the IP address is, you are broadcasting this to the world. 

There are some more secure versions now that you can use to keep this information safe. One is called `DNS over HTTPS (DoH)`. What this does is instead of sending data unencrypted over port 53. It is not encryted through HTTPS. 

A similar one to this is `DNS over TLS (DoT)`.

-----

### Virtual Private Network (VPN)

A VPN allows you to connect from point A to point B through an encrypted tunnel. This allows you to access another network that has some features that you are only able to access if you were on that network.

This does not prevent you from being hacked. VPNs allow you to apear in a diffrent country by connecting you to a an IP address that is in a diffrent contry. This means when you search a service, they will think you are in that location.

-----

### TOR

This is software you can install that uses encryption to give you a higher level of security. TOR is known as the `onion` browser because it routes your browser search through other devices that are also using TOR. This means that as it is passed to another device, it picks up that public key and encrypts the data, then it is sent to another device where it pickes up that devices public key and encrypts the data, and so on. Essentially layering the encryption. 

This isnt to say that it is 100% secure but it is extremely high security.

-----

### Permissions

Mostly seen in phones, it is now increasing becoming common for your device to ask you if you give permission for the site or service to have access to certain data or even your camera. 

This is great, becuase you can deny access and get more control over what's being viewed. But all this is doing is moving the problem down the road becuase if you want access to certain features, you will need to accept these terms. 

Something that may need to run all the time is `location-based services`. This is things like maps and other programs that require your location. If these services are left on all the time, then these services know where you are at all times.





















