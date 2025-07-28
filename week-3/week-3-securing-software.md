## CS50 Cybersecurity week 3 - Securing software

### Notes from lecture

Today we are taking a look at securing software. First let's look at phishing. Let's look at how phising is created and how someone can spot whether an email and link are phishing. 

The best way to spot phishing links is to hover over links before clicking on them, with most browsers, this will show you where this link is actually taking you to in the bottom left corner of your browser.

So, what is happening in the HTML elements. Let's say you wanted a link on a webpage to go to `Harvard.edu`, here is how you would do that:
```
<a href="https://harvard.edu">Harvard.edu</a>
```

This link on a website, when clicked, would take you to `Harvard.edu`. When you hover over the link, it will also show `https://harvard.edu`.

Now, let's look at what someone (not really an attacker) could do:
```
<a href="https://yale.edu>https://harvard.edu</a>
```
This, again, is not really an attack, but what would happen now is the link would look like `https://harvard.edu` but if you were to hover over the link or even click the link, it will actually take you to `https://yale.edu`.

The actual danger comes when an attacker creates a fake version of Harvard, for example `https://harva1d.edu` and instead of linking you to the actual harvard site, they send you to this site instead where they have copied the actual Harvard site almost identiacally, but the only diffrence is they are actually hoping you sign in so that they can collect your data and password. 

There is another attack called a `stored` attack. A stored attack saves the users input (even if it inlcudes HTML or JavaScript). Suppose an adversary is using gmail and they send an email with:
```
<script>alert('attack')</script>
```
This email is then saved on a server until the receipent reads or deletes the email. The issue would be that if Google hadn't pretected its users from this attack, when the recipient opens this email, the attack would be run.

Ideally, we would rather the indox to show us the code as text without interpreting the code and without running it.

To prevent this kind of attack, you can use what's called `character escapes`. This takes any users input that could be misenterperated or dangerous. What would be concidered dangerous could be something like a "less than" symbol. This could be mistaken as the start of a HTML tag.

Software should then be escaping these characters by using standardised special sequences that represent these symbols. What this means is that the browser fro example in a email can see this special sequence and know not to print the sequence but the symbol represented by that sequence. for example:
```
// Raw data sent across
<p>About 6,420,000,000 &lt;script&gt;alert('attack')&lt;/script&gt;</p>

// prined data
<p>About 6,420,000,000 <script>alert('attack')</script></p>
```

- `&lt;` = < (less than)
- `&gt;` = > (greater than)
- `&amp;` = &
- `&quot;` = "
- `&apos;` = '


How else can we prevent attacks like these? We can add a HTML header that looks like this:
```
content-security-policy: script-src https://example.com/
```

What this means is it will only read JavaScript from the script source file stored on the web server. This prevents inline script attacks.

There is something similar that happens with `CSS` that looks like this:
```
content-security-policy: style-src https://example.com/
```

-----

### SQL Injection

SQL is a language commonly used to communicate with databases. SQL is commonly used with python. When you log in to a site that's using SQL and python, what might happen is you submit your username and password and the python sends a request using SQL to check if the username and password are valid inside of the database.

What could potentialy go wrong is that the user could input something malicious. To prevent this, we need to `sanitise` user inputs. To do this, we are going to escape the characters again like we did before. 

Let's take a look at an example of what an attacker might do to attack this database by injecting their own code in SQL.

Instead of inputing a username in the username bar such as `malan`. Instead, we enter something like:
```
malan'; DELETE FROM users; --
```

Let's break that down:
- The single quote after `malan` is to tell SQL that is the "end" of the input from the user.
- The semicolan ends that part of the code and allows us to put more code after on the same line.
- The `DELETE FROM users;` will delete all users from the data base called `users`.
- The `--` is to comment out any other code in the query. In this case it will block out the single quote that was supposed to be put after the username input.

Here is what the entire query could look like with this malicious code added:
```
SELECT * FROM users WHERE username = 'malan'; DELETE FROM users; --'
```

Let's take a look at another one that's a little more common. When you make a log in request on a website, you are not likly just entering a username, you are most likely entering a password at the same time to submit under the same request to the database. Here is what that might look like:
```
SELECT * FROM users WHERE username = '{username}' AND password = '{password}';
```

Here it is still possible to inject our own code because we are using placeholders where we are going to blidely plug in the text that the user inputs. What that means is someone could input this into the username and password:
```
Username: malan
password: ' OR '1'='1
```

That would then turn the request to the data base like this:
```
SELECT * FROM users WHERE username = 'malan' AND password = '' OR '1'='1';
```

This then tells the database, get me the information for all users where the username is `malan` and the password is equal to nothing. Or if 1 = 1 then give me the information. 

This will therefor return everything in the database.

The solution is `prepared statements`. What we can do is use `?` in the query as placeholders. That way it removed the need for using `'` which run the risk of being attacked. The placeholder request looks like this:
```
SELECT * FROM users WHERE username = ?
```

If the user now input's the malicious code like before, the request will now look like this instead:
```
SELECT * FROM users WHERE username = 'malan''; DELETE FROM users; --'
```

As you can see the double apostrophe cancels out the rest of the malicious code.

Let's now look at the second statment and see how this now looks:
```
SELECT * FROM users WHERE username = ? AND password = ?
```

When the attacker then sends the malicious code, it looks like this:
```
SELECT * FROM users WHERE username = 'malan' AND password = ''' OR ''1''=''1'
```

There is another similar type of injection called `command injection`. Command line injection is where the users input is sent to the systems comman line interface and attacks can be done on the system. This is very dangerous becuase the attacker can not only delete files and things from the database, but they can also control your device and send mail.

In other programming languages, there is another function called `eval`. This function evaluates what you send to it. 

If you don't sanitise the data before it is send to system or eval, then you are putting your device and data at risk.

The solution is to use a function that santises the data for you before it is sent. 

-----

### Developer tools

Within a browser, you have what's called `developer tools`. Here you can see all the HTML, javascript, and the CSS that is making up the website. With these tools, let's look at a check box made with HTML:
```
<input disabled type="checkbox">
```

As you can see the type is a `checkbox` and the checkbox is disabled. This means you can see it, but you dont want to give access to the user to use it.

The issue is, This HTML is being sent to every users device. So if they wanted to, they can edit this HTML (Not on the server, so it wont be perminent) but they can edit the disabled and make this option available to them. 

Usualy this isnt a big deal, but if you are diabling this things to prevent the user from being able to agree to something or use something on a form when it is submitted. If they change this and submit, it will allow them to.

The issue is you are relying on the user being honest which is something you should never do. To get around this, we need to do some server side validation.

-----

Let's now look at another type of attack that is vulenerable on HTML called `Cross-site request forgery (CSRF)`. If you recall. HTTP uses `GET` to get data from the server to display information on the website. It's important to know, when get is used, all the information is inside of the URL. Here is a link on amazon that allows you to buy the product imediatly instead of needing to add the item to your basket and go through the delivery steps:
```
<a href="https://www.amazon.com/dp/B07XLQ2FSK">Buy Now</a>
```

So why can this be malicious? Well, say someome took that link and created a website and used that link instead as an image tag. Like this:
```
<img src="https://www.amazon.com/dp/B07XLQ2FSK">
```

Now, eveytime you load this website, you will buy this product becuase the browser will process this url. Thankfully, amazon doesnt use this method. There is also a more secure way of sending data over the internet known as `POST`. Post is used to send data that you don't want visable in the url bar, such as payment information and password information.

Post would also be used for images and videos to be posted as they don't really fit in the url and so its much easier to use post.

"Post is meant to change state on the server"

Unfortunatly, post is still able to be attacked. Here is a better implementation of that "Buy now" feature using a form from amazon:
```
<form action="https://www.amazon.com/" method="post">
  <input name="dp" type="hidden" value="B07XLQ2FSK">
  <button type="submit">Buy Now</button>
</form>
```

As you can see, have an input called `dp` with the value of the product number and we have hidden this element so that the user can't see it in the URL.

This does fix the issue we had with `get` and attackers beign able to put the link in the website as an image because a form cannot be submitted through a website. But what they can do is add this form to their site and use this JavaScript to post the form once you load the site:
```
<form action="https://www.amazon.com/" method="post">
  <input name="dp" type="hidden" value="B07XLQ2FSK">
  <button type="submit">Buy Now</button>
</form>
<script>
  document.forms[0].submit();
</script>
```

This is what's known as cross-site request forgery becuase it is being sent across sites and it is forgery becuase it is the attacker that is actually making these requests.

So, how do we stop this `post` attack. We can use a `token`, commonly known as a `csrf_token`, inside of the form request that is randomly generatred by the server. The benifit is that each value of this token is unique to each user. This means that the attacker cannot use the previous methods of attack because they wont be able to get this number (unless they hack amazon first). Here is what this form would now look like:
```
<form action="https://www.amazon.com/" method="post">
  <input name="csrf_token" type="hidden" value="1234abcd">
  <input name="dp" type="hidden" value="B07XLQ2FSK">
  <button type="submit">Buy Now</button>
</form>
```

If you want to see more of these attacks, you can check out the `Open Worldwide Application Security Project (OWASP)`.

-----

Let's now look at attacks that are more focused towards the software that's on your mac, pc, or phone. 

The first we are going to look at is called `Arbitrary Code Execution (ACE)`. This is where the attacker runs code on your device that is malicious and is not built into the software on your device. This is also known as `Remote Code Execution (RCE)` which is where the attacker can do this from anywhere in the world. How are these attacks done? They are very commonly done through what's called `Buffer Overflow`. 

Buffer overflow is when a program is running, the computer needs more memory for example the users input. But if an attacker inputs a large attack code, so much that it overflows other parts of the program in memory, specifiacally the retrun adress and they manage to make the program return the the attackers code, then they can get the system to run some code that will then take over your device. 

This is known as `cracking`. Cracking a device means you can take control of the device.

There is a good side of reverse engineering called `malware analysis`. This is where you figure out what the malicious software does and how it was made. That way we can figure out a way to prevent it or fix it after it has been run.

One way to prevent against these attacks is using `open-source code` apps and software. What this means is that the code is open for the user to read before installing so that you can see what is going to be executed and installed on your device. This doesnt mean that an attack isnt possible. The code may have bugs that the attacker might be able to find by reading through the software and find a way to attack.

Another method agaist this is `closed-source software`. This is where the code that you get is hidden from the users and therfor the attacker so that it makes it much harder to find bugs in the software. 

A way to insure that your software you are downloading is not malicious is to download it from an `app store`. This is because companies like Apple will vet the software to check that it is not malicious before allowing the public to download it to their devices. This is not to say it is perfect, but it's much better than installing from a site.

How do app stores like Apple make sure that you can only install software from the app store. What happens is, the company uploads the software to the app store. Apple will then hash the software, then add their private key and the hash to get a signature. Then the software can be checked via this signature to make sure that it is safe to download as it is verified by the app store.

Something that is similar but mostly used with software like linix is `Packet managers`. Packet managers allow us to install programs using things like `pip`. What they are doing to signing the software so that the device can verify the software before installing and running it. 

Operating softwares are also bringing in native ways to do this.

This is also not fail safe, as there may be some bugs in the code. Sometimes the attackers will seem like a good software developer and will wait till the thrist update or more to actualy deploy their malicious plans. A software company may sell their company and the new owners want to turn the code malicious. Maybe the company is hacked and the software is attacked and pushed to users via an update. But the cost of this to the attacker is so high that it is unlikely.

What companies have done is allow `bug bounties`. What this means is you can find bugs for the company and you disclose it to the devs, they will then pay you for finding the issue. 

You can learn more about threats at `Common Vulnerabilities and Exposures (CVE)`. There is also a scoring system for these vulnerabilities which are called `Common Vulnerabilities Scoring System (CVSS)`. There is a exloit predition system called `Exploit Prediction Scoring System (EPSS)` that people make probabilities of the vulnerabilities in the software. There is also a catalog of all the exploited vulnerabilities at `Known Exploited Vulnerabilities Catalog (KEV)`.
