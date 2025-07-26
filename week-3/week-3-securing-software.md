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
















