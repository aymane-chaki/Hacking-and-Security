---
created: 2021-03-29T17:57:05 (UTC +01:00)
tags: []
source: https://blog.appsecco.com/static-analysis-of-client-side-javascript-for-pen-testers-and-bug-bounty-hunters-f1cb1a5d5288
author: Bharath
---

# Static Analysis of Client-Side JavaScript for pen testers and bug bounty hunters | by Bharath | Appsecco


[![Bharath](https://miro.medium.com/fit/c/96/96/0*lsXqlEYwUTc_Pksh.jpg)](https://medium.com/@0xbharath?source=post_page-----f1cb1a5d5288--------------------------------)

JavaScript has become one of the most ubiquitous technologies in the modern web browsers. Applications built using client-side JavaScript frameworks such as AngularJS, ReactJS and Vue.js push a lot of functionality and logic to the front-end.

> With the increased functionality/logic on the client-side, the attack surface on the client-side also increases.

As a security tester it is essential for us to understand the attack surface of these applications. It is also important for us to know what information to look for, where to look for and how to look for the information that will lead to the discovery of potential security issues in the applications.

In this blog post we will look at performing static analysis of client-side JavaScript code in order to uncover potential security issues in the applications.

> In this blog post we are particularly interested in performing static analysis to uncover security issues. We will not delve into performance analysis or functional testing.


![](https://miro.medium.com/max/2424/1*Jm1f5iF-jXygKoOY9_HJaA.png)

Static analysis is analysing code without executing it.

As a penetration tester, when we are performing static analysis of client-side JavaScript, we are more or less interested in the information that will fall under following categories —

1.  Information that’ll increase the attack surface (URLs, domains etc)
2.  Sensitive information (Passwords, API keys, Storage etc)
3.  Potentially dangerous areas in code(eval, dangerouslySetInnerHTML etc)
4.  Components with known vulnerabilities (Outdated frameworks etc)

We’ll perform static analysis by breaking it down into following steps:

1\. Identifying and gathering JavaScript files in an application  
2\. Making the gathered JavaScript code readable (Unminify/Deobfuscate)  
3\. Identifying information that may lead to discovery of security issues

1.If you use Burp Suite for testing applications then there are multiple ways to gather all the JavaScript files in an application.

> At Appsecco, We follow user-driven workflow for testing web applications where we start testing by navigating the entirety of the application like an end user would do.

Navigate through an application while the traffic is being sent through Burp proxy. Once you are done with the navigation, you can use Burp’s tool-set to extract all the JavaScript files.

-   If you are using Burp Suite Community Edition, you can navigate to `proxy > HTTP history` and use the display filters to only display the JavaScript files used by the application. You can also copy the URLs for all the JavaScript files displayed.

![](https://miro.medium.com/max/60/1*8YLktaI23nHogmLT175ywg.png?q=20)

![](https://miro.medium.com/max/1864/1*8YLktaI23nHogmLT175ywg.png)

Burp display filters to display only JavaScript files for a given application

![](https://miro.medium.com/max/2290/1*2AzEsdFW7ymvijuZV593Bg.png)

Copy the URLs for all the JavaScript file displayed after filtering

-   If you are using Burp Suite Professional, You can not only copy the URLs for all the JavaScript files in an application but also export all the scripts. Under `Target > Site map` right click on the site of interest and select `Engagement tools > Find scripts` . Using this feature you can export all the scripts in that application and also copy URLs.

![](https://miro.medium.com/max/1918/1*Asd2jV3vCOX55BzZLirdMA.png)

Burp “Find Scripts” to identify all the JS files on an application

![](https://miro.medium.com/max/1978/1*q27kCqV73EHVah3T-uwZRA.png)

Burp “Find scripts” can export all the scripts, not just URLs

2. Another interesting technique to quickly list JavaScript files in an application is to dig through internet archive databases such as [Wayback Machine](https://archive.org/web/). This technique is completely passive because we are not sending any request to the target application server.

> _Digging through internet archives such as_ [Wayback Machine](https://archive.org/web/) _is quite useful in identifying JavaScript files in an application. Sometimes you’ll be able to find older JavaScript files that are not deleted on the server._

-   [waybackurls](https://github.com/tomnomnom/waybackurls/) is a neat tool to search for JavaScript files(or any other URLs for that matter) that belong to a site of interest using [Wayback Machine](https://archive.org/web/)

go get github.com/tomnomnom/waybackurls  
waybackurls internet.org | grep "\\.js" | uniq | sort

![](https://miro.medium.com/max/2556/1*2U4rw-JOF5sFEWyEsJdPxQ.png)

Using “waybackurls” to extract URLs for JavaScript files that belong to a domain that are listed in Wayback Machine archive

-   Using [Wayback Machine](https://archive.org/web/) might result in false-positives i.e. JavaScript files that don’t exist on the server anymore. Once you have gathered the list of URLs for JavaScript files, you can use [curl](https://ec.haxx.se/usingcurl.html) to quickly check for the status of the JavaScript files on the server

cat **_js\_files\_url\_list.txt_** | parallel -j50 -q curl -w 'Status:%{http\_code}\\t Size:%{size\_download}\\t %{url\_effective}\\n' -o /dev/null -sk  

![](https://miro.medium.com/max/3254/1*3ThluG1AInGCFLaJPfnjjQ.png)

Using [cURL](https://ec.haxx.se/usingcurl.html) to quickly check for the status of the JavaScript files on the server

-   Using curl to check for the status of the JavaScript files on the server could be cumbersome. There are utilities that make the process of finding the status of files straight forward. [hakcheckurl](https://github.com/hakluke/hakcheckurl) is one such utility written in Golang that I use often for it’s simplicity and speed.

go get github.com/hakluke/hakcheckurl  
cat lyftgalactic-js-urls.txt | hakcheckurl

![](https://miro.medium.com/max/2244/1*QZdYdFsEmOBBzd3SiMtMJg.png)

Using hakcheckurl to find status of files on the server

Sometimes the JavaScript files you gathered might not be immediately intelligible or readable. This can happen because the developer might have minified or obfuscated the JavaScript code.

> [Minification](https://developers.google.com/speed/docs/insights/MinifyResources) refers to the process of removing unnecessary or redundant data without affecting how the resource is processed by the browser — e.g. code comments and formatting, removing unused code, using shorter variable and function names, and so on.
> 
> Obfuscation involves making modifications to the program, changing the names of variables, functions, and members, making the program much harder to understand.

-   There are various tools that can minify JavaScript. [UglifyJS](https://github.com/mishoo/UglifyJS) is a neat tool to minify JS code and it also available as npm package

![](https://miro.medium.com/max/2006/1*CYJ2_yGXWT91kxoGTMdgRQ.png)

Minify JavaScript using [UglifyJS](https://github.com/mishoo/UglifyJS2)

-   There are various tools that can un-minify JavaScript. [JS Beautifier](https://github.com/beautify-web/js-beautify) is a tool that can beautify and also deobfuscate certain obfuscation schemes. You can use this tool via [node.js](https://www.npmjs.com/package/js-beautify), [python](https://pypi.org/project/jsbeautifier/), [online](https://beautifier.io/) or in a code editor like [VS Code](https://github.com/HookyQR/VSCodeBeautify).

![](https://miro.medium.com/max/2096/1*Yk_g9ivV9SipOqVftp5DCw.png)

Unminify JavaScript using [JS Beautifier](https://github.com/beautify-web/js-beautify)

When performing deobfuscation, especially when reversing malware there is no one size fits all technique or tool. You’ll have to try various tools, deobfuscation schemes and perform some manual analysis. There are various tools out there that can help you deobfuscate JavaScript code. Some of the tools we often use are [JStillery](https://github.com/mindedsecurity/JStillery), [JSDetox](http://relentless-coding.org/projects/jsdetox), [JS-Beautifier](https://github.com/einars/js-beautify), [IlluminateJs](https://github.com/geeksonsecurity/illuminatejs), [JSNice](http://www.jsnice.org/) etc.

1.One of the key information to look for in JavaScript files is endpoints i.e full URLs, relative paths etc.

> Identifying full URLs, relative paths in JavaScript files will help us uncover the attack surface and potentially find more vulnerabilities

-   [relative-url-extractor](https://github.com/jobertabma/relative-url-extractor) by [Jobert Abma](https://github.com/jobertabma/) is quite handy to quickly identify all the relative paths in a JavaScript file. This tool can work on both local and remote JavaScript files. This tool can work directly on minified JavaScript

-   [LinkFinder](https://github.com/GerbenJavado/LinkFinder) by [Gerben Javado](https://github.com/GerbenJavado) is quite handy in identifying all the endpoints and their parameters in JavaScript files. This tool can also work directly on minified JavaScript and it uses `jsbeautifier` to unminify JavaScript. This tool can also be run against a domain to enumerate all the JavaScript files

python linkfinder.py -i [https://e](https://swiggy.com/)xample.com -d -o cli

![](https://miro.medium.com/max/3300/1*MJ6LXXjIUwE0T-mdrScLQw.png)

Using [LinkFinder](https://github.com/GerbenJavado/LinkFinder) to extract endpoints from all the JS files in an application

2.JavaScript files might also contain sensitive information such as credentials and API keys. Broadly speaking, we can identify secrets in source code files using either regex or entropy. Regex search will be able to identify credentials that are set by users such as usernames and passwords. Entropy based search is effective in identifying sufficiently random secrets such as API keys and tokens.

-   [DumpsterDiver](https://github.com/securing/DumpsterDiver)[,](https://github.com/dxa4481/truffleHog) [Repo-supervisor](https://github.com/auth0/repo-supervisor#repo-supervisor) [and truffleHog](https://github.com/dxa4481/truffleHog) are some fantastic tools to search for secrets in source code files. Most of these tools support both entropy based and regex based search. They are also easily and highly customisable both in regex search and entropy search.

![](https://miro.medium.com/max/3800/1*Ibf1jeE2kpgjPIzMcJ0tnw.png)

-   Don’t forget that _grep/sed/awk_ are also quite powerful when searching for specific sensitive information the source code files.

3. When looking into JavaScript files, it is important to identify dangerous places in code i.e. places where developers tend to make mistakes that will lead to potential security issues.

-   Usage of `innerHTML` indicates that there might be possible XSS issue. In the modern client-side JavaScript frameworks `innerHTML` equivalents do exist such as the aptly named `dangerouslytSetInnerHTML` in React framework and they did result in [serious security vulnerabilities in the past](https://thehackerblog.com/i-too-like-to-live-dangerously-accidentally-finding-rce-in-signal-desktop-via-html-injection-in-quoted-replies/index.html) . Improper usage of `bypassSecurityTrustX` methods in Angular can also lead to XSS issues. `eval` function is another place where things can go wrong both on client-side and server side.

![](https://miro.medium.com/max/2252/1*VJLKbFxI4bjk-JQ8oyOqpQ.png)

`List of bypassSecurityTrustX` methods in Angular

-   [_postMessage_](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) API is an alternative to JSONP, XHR with CORS headers and other methods enabling sending data between origins by bypassing Same Origin Policy(SOP). The idea of bypassing SOP and communicating with different origin should be of interest to attackers. There are various [security pitfalls](https://labs.detectify.com/2016/12/08/the-pitfalls-of-postmessage/) when using postMessage. Once you understand the possible security issues associated with postMessage, you can look for the implementation in JavaScript files. On the message sender side, look for `window.postMessage` and on the receiver end look for a listener `window.addEventListener` . You’ll have to keep in mind that a lot of frameworks implement wrappers around postMessage
-   localStorage and sessionStorage are HTML Web Storage Objects. With web storage, web applications can store data locally within the user’s browser. It is important to identify what is being stored using the Web Storage especially storing anything sensitive can lead to potential security issues. In the JavaScript, you can look for `window.localStorage` and `window.sessionStorage`.

Looking for dangerous places in JavaScript that might lead to potential security issues is highly dependent on stack that is being used. You should identify what frameworks are being used, identify dangerous functions in the framework and then look for them in the source code.

5. Using security linters and static security scanners will make it easy to identify low hanging vulnerabilities in JavaScript code. [JSPrime](https://github.com/dpnishant/jsprime) is a static analysis tool for finding security issues in JavaScript code but the project hasn’t been updated in a while. [ESLint](https://github.com/eslint/eslint) is one of the most popular JavaScript linters. ESLint is easily customisable by adding custom rules. A lot of custom [security rules](https://github.com/LewisArdern/eslint-plugin-angularjs-security-rules) are available for ESLint especially targeting modern frameworks like Angular, React etc.

![](https://miro.medium.com/max/3294/1*7_KdLVGWUWylX6CoHHignQ.png)

ESLint with Angular security rules reporting potential security issues

6. When performing security assessments, it is important to identify old and vulnerable JavaScript frameworks/libraries being used in the application. [Retire.js](https://retirejs.github.io/retire.js/) is a tool that can identify outdated JavaScript frameworks in use. This tool can be used as a stand alone tool, browser extension, grunt plugin or Burp/ZAP extension. RetireJS Burp extension is especially useful if you are performing your testing in a user-driven manner. Although RetireJS can report some false positives and not everything reported by RetireJS is vulnerable

![](https://miro.medium.com/max/2784/1*yQ9ZE5hqtryY_wCYcoiQSQ.png)

RetireJS Burp suite extension reporting an outdated JavaScript library

_At_ [_Appsecco_](http://appsecco.com/) _we provide advice, testing, training and insight around software and website security, especially anything that’s online, and its associated hosting infrastructure — Websites, e-commerce sites, online platforms, mobile technology, web-based services etc._

![](https://miro.medium.com/max/1600/1*aeQGhZfIN2zfot7ICDBQyA.jpeg)
