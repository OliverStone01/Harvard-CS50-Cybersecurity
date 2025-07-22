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











