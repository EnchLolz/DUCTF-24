# Hah Got Em

Category: Web (Easy)

Points: 129

Solves: 173

>>Deez nutz
>>
>>Hah got em
>>
>>...
>>
>>Oh by the way I love using my new microservice parsing these arrest reports to PDF
>>
>*The evil bot (2024)*

### Solution

All we get is a Dockerfile:

```docker
FROM gotenberg/gotenberg:8.0.3

COPY flag.txt /etc/flag.txt
```

What? After a bit of reasearch we find that [Gotenberg](https://gotenberg.dev/) is an api that can turn url, html file, and md file content into pdfs or images. Following the documentation we can try to convert the DUCTF website into a pdf.

![Test](/images/HahGotEmTest.png)

Nice, it looks like that works. My next thought was that we could simply just use the file uri and call ```file:///etc/flag.txt``` but unfortunately that lead to a blank pdf. After a while of random testing, I decided to lookup the [Gotenberg source code](https://github.com/gotenberg/gotenberg). The most recent version was `8.8.0` but the challenge used `8.0.3` so I scrolled back through the releases and found why:

![Release 8.0.3](/images/HahGotEmRelease.png)

Looks like `8.0.3` is right before they patched a bug that allowed for LFI (Local File Inclusion). After a while of trying to find a post demonstrating the vulnerability, I gave up and decided to look at the changes between `8.0.3` and `8.1.0`. One of the first changes caught my eye:

![Commits](/images/HahGotEmCommit.png)

They changed the regex for chromium deny list, extremely suspicious. Seems like they banned going to `file:///[^tmp].*`. Well, bypassing the `[^tmp]` just requires the first char of the path to not be a `t`, `m`, or `p`. So, I tried using `file:///t/../etc/flag.txt` but this gave nothing. It turns out that when accessing these urls, the browser will first format the file uri, removing extra slashes and condensing down `..`.

After a bit of research on the file uri (https://en.wikipedia.org/wiki/File_URI_scheme). It turns out that the uri in full is ```file://host/path```, but most people leave out the host as it's automatically assumed to be `localhost`. However, we can still put in `localhost` and get a file. Furthermore, `file://localhost/etc/flag.txt` doesn't match the regex `file:///[^tmp].*` due to the third slash coming after `localhost`.

With our new payload we can get the flag:

```shell
curl --request POST "https://web-hah-got-em-20ac16c4b909.2024.ductf.dev/forms/chromium/convert/url" \
--form url=file://localhost/etc/flag.txt \
-o out.pdf \
```

![Payload](/images/HahGotEmPayload.png)
![Flag](/images/HahGotEmFlag.png)

Note: The intended solution for this challenge was to use the `proc` virtual file system in linux as the `p` in `proc` would not match with `[^tmp]`. We could then send `file:///proc/self/root/etc/flag.txt` and also get the flag. (You can replace self with any pid you would like to guess.)



### Flag

```DUCTF{dEeZ_r3GeX_cHeCK5_h4h_g0t_eM}```