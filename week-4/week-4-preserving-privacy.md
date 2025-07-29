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



























