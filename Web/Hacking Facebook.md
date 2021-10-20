---
created: 2021-03-29T01:48:21 (UTC +01:00)
tags: []
source: https://infosecwriteups.com/how-i-hacked-facebook-part-one-282bbb125a5d
author: Alaa Abdulridha
---

# How I hacked Facebook: Part One.
We’ve been in this pandemic since March… | by Alaa Abdulridha  We’ve been in this pandemic since March and once the pandemic started I was having plenty of free time, And I need to use that time wisely, So I’ve decided to take the OSWE certification and I finished the exam on 8 of August, after that, I took a couple of weeks to recover from the OSWE exam, then in the med of September, I said you know what? I did not register my name in the Facebook hall of fame for 2020 as I do every year. okay, let’s do it.

I never found a vulnerability on one of Facebook subdomains, and I took a look at some writeups and I saw one writeup in one of Facebook subdomains which got all my attention It was a great write up you can check it out [\[HTML to PDF converter bug leads to RCE in Facebook server.\]](https://ysamm.com/?p=280)

So after reading this write-up now I took a good idea about how many vulnerabilities I could find in such a huge web app.

So my main target was [**https://legal.tapprd.thefacebook.com**](https://legal.tapprd.thefacebook.com/) and my goal was RCE or something similar.

I ran some fuzzing tools just to get the full endpoints of this web app and I took a 2 hours nap and watched a movie, Then I got back to see the results okay I got some good results.

**Dirs found with a 403 response:**

Dirs found with a 403 response:/tapprd/  
/tapprd/content/  
/tapprd/services/  
/tapprd/Content/  
/tapprd/api/  
/tapprd/Services/  
/tapprd/temp/  
/tapprd/logs/  
/tapprd/logs/portal/  
/tapprd/logs/api/  
/tapprd/certificates/  
/tapprd/logs/auth/  
/tapprd/logs/Portal/  
/tapprd/API/  
/tapprd/webroot/  
/tapprd/logs/API/  
/tapprd/certificates/sso/  
/tapprd/callback/  
/tapprd/logs/callback/  
/tapprd/Webroot/  
/tapprd/certificates/dkim/  
/tapprd/SERVICES/

Okay, I think this result is very enough to support my previous theory about how huge this application, Then I started to read the javascript files to see how the website works and what methods it uses ..etc

I noticed a way to bypass the redirection into the Login SSO, [https://legal.tapprd.thefacebook.com/tapprd/portal/authentication/login](https://legal.tapprd.thefacebook.com/tapprd/portal/authentication/login) and after analyzing the login page, I noticed this endpoint

**/tapprd/auth/identity/user/forgotpassword**

and after doing some fuzzing on the user endpoint I’ve noticed another endpoint which its **/savepassword** and it was expecting a POST request, Then after reading the javascript files I knew how the page work, there should be a generated token and xsrf token.. etc The idea that first came to me okay, Lets test it and see if it will work I tried to change manually using burp suite but I got an error, the error _was execution this operation failed_.

I said okay, this might be because the email is wrong or something? let’s get an admin email, Then I started to put random emails in a list to make a wordlist and after that, I used the intruder and I said let’s see what will happen.

I got back after a couple of hours I found the same error results plus one other result, This one was 302 redirect to the login page, I said wow, I’ll be damned if this worked Haha.

So let’s get back to see what I’ve done here, I sent random requests using intruder with a CSRF token and random emails with a new password to this endpoint **_/savepassword_**

and one of the results was 302 redirect.

![](https://miro.medium.com/max/3342/0*tAHufKVT1uSATSFZ.png)

**Now I went to the login page and I put the login email and the new password and BOOM I logged in Successfully into the application and I can enter the admin panel :)**

![](https://miro.medium.com/max/3570/0*ulUllcbvnAMG5IRa.png)

I read the hacker report who found RCE before using the PDF and they gave him a reward of 1000$ only so I said okay, let’s make a good Impact here and a perfect exploit.

I wrote a quick and simple script to exploit this vulnerability with python you put the email and the new password and the script will change the password.

![](https://miro.medium.com/max/1966/0*iC51m4ovlT3Ld453.png)

**The Impact here was so high because the Facebook workers used to login with their workplace accounts, Which mean they’re using their Facebook accounts access token, and maybe if another attacker wanted to exploit this it might give him the ability to gain access to some Facebook workers accounts .. etc**

Then I reported the vulnerability and the report triaged.

**And on 2 of October, I received a bounty of 7500$**

![](https://miro.medium.com/max/1392/0*IPYrnTFW_hYbXPOr.png)

I enjoyed exploiting this vulnerability so much, so I said that’s not enough, this is a weak script! let’s dig more and more.

And I found two more vulnerabilities on the same application, But we will talk about the other vulnerabilities in the Part two writeup :)

You can read the write-up on my website : [https://alaa.blog/2020/12/how-i-hacked-facebook-part-one/](https://alaa.blog/2020/12/how-i-hacked-facebook-part-one/)

And you can follow me on twitter : [https://twitter.com/alaa0x2](https://twitter.com/alaa0x2)

Cheers.

# How I hacked Facebook: Part Two. 
This is the second and final part of How I hacked Facebook you can find part one here [[Hacking Facebook#How I hacked Facebook Part One]].

---
> _This article was previously revised by multiple parties before releasing it, and I had to get written permission to release it as well so that some names and information might be changed as a request from Facebook and its partners._

So when I found the first vulnerability in part one Facebook mitigated it after a day of reporting it.. then I had to get back to read the burp suite history and just to see how things worked.


![](https://miro.medium.com/max/3802/0*grOfcndjJtYQgPSs.png)

As you can observe in the screenshot, Number 1 in blue, the cookies its **ASPXAUTH,** Absolutely yes!.

Have you got what I imply? — **ASPXAUTH** indicates 80% it’s vulnerable but first, you need several things:

-   `**validationKey**` (string): hex-encoded key to use for signature validation.
-   `**decryptionMethod**` (string): (default “AES”).
-   `**decryptionIV**` (string): hex-encoded initialization vector (defaults to a vector of zeros).
-   `**decryptionKey**` (string): hex-encoded key to use for decryption.

You can read more about that here: [**MachineKey Class**](https://docs.microsoft.com/en-us/dotnet/api/system.web.security.machinekey?view=netframework-4.8)

well, I don’t have these 4 things, so how did I assume it’s vulnerable? — Okay. Actually, I didn’t but most of the application that uses the **ASPXAUTH** they use only email or user in the encrypted cookies with the encryption keys, and expiration time. I exploited it in other bounty programs websites using the same method many times before, and it worked.

So here I have to find a way to go around this, and I believe there’s nothing to lose by trying, after that, I proceeded to google and searched for other websites that utilized the same application — I assumed here that I will be lucky and find a website that utilizes the same application and same encryption keys and I just have to use the correct admin username.

I did that and I found another website using the same application and the registration is active and I registered using a username that is used by the Facebook admin, I intercepted the request and took the **ASPXAUTH** and replaced it with the Facebook expired **ASPXAUTH**, and guess what?

![](https://miro.medium.com/max/3530/0*nRTOw93otUixHtgy.png)

![](https://miro.medium.com/max/996/0*kTKnpDRhw5Scqqc6)

I missed this Panel for a while :) .. but yeah! I’m back into it again, now let’s have a little talk about **ASP.net** mistake that most developers must be careful while building their applications to secure it according to these several points::

-   The **ASPXAUTH** must be stored in the database and the application must check if it’s valid.
-   The **ASPXAUTH** sometimes must contains more than the username as further validation.
-   The Encryption and decryption keys must be different from site to site (must change the default keys).

**Conclusion 1**: I was able to login using any admin account just by knowing the username, the complexity of this vulnerability I consider it very **low** and the impact is **high,** If I report only this vulnerability I will get **$7500** as the first part but I wanted more.

So I just noticed something in the panel which’s the option of making forms, and there was another option which’s **API trigger**. So, I suspected something, mostly there’s an [**SSRF**](https://portswigger.net/web-security/ssrf) here with no limits at all, according to that I wrote a message to the Facebook Security Team explaining to them my suspicion there’s almost certain critical [**SSRF**](https://portswigger.net/web-security/ssrf) in the application, and if I can get permission to test it, and they answered me :

![](https://miro.medium.com/max/1388/0*2uvsX07AsM_JPmGo.png)

at this point, I was still in contact with them in the report of the first part (The account takeover), because these vulnerabilities were reported a week after the first one. As you can see Facebook Security Team still thinks that I’m claiming that I have another auth bypass and SSRF even after I explained the vulnerabilities to them with proofs. According to that, it means they gave me the green light to test the [**SSRF.**](https://portswigger.net/web-security/ssrf)

After a while, I wrote a small script and uploaded it to their editor, the script allowed me to send any request I want with any data (**GET, POST, PUT, PATCH, HEAD, OPTIONS**) to any URL — **internal** or/and **external**.

![](https://miro.medium.com/max/1428/0*AMXsCO0asKXla0R9.png)

From the script backend, I was able to change the request method and the sent data, etc

at this point, I was able to escalate this vulnerability to **RCE, LFI** if I go a little further maybe (_I’m not really 100% sure about this point, I asked Facebook later to grant me the permission to reverse engineer the app but they did not accept, and they believe I won’t be able to escalate it_).

And I tried to hit the Facebook canary script :), again guess what?

![](https://miro.medium.com/max/3364/0*HFgP-23uls1eC7Ih.png)

I got my lucky Canary token, Now what’s next? — I have to make a new report with the full details including the scripts and PoC as they mentioned before.

**Conclusion 2:** By writing a script to send custom-crafted requests I was able to gain an Internal [**SSRF**](https://portswigger.net/web-security/ssrf) and gain access to the Facebook internal network, The complexity here I consider it low and the Impact is **Critical**.

> _The Full Impact of this SSRF :_
> 
> _A successful SSRF attack can often result in unauthorized actions or access to data within the Facebook internal network, either in the vulnerable application itself or on other back-end systems that the application can communicate with. In some situations, the SSRF vulnerability might allow an attacker to perform_ [_arbitrary command execution_](https://portswigger.net/web-security/os-command-injection)_._
> 
> _An SSRF exploit that causes connections to external third-party systems might result in malicious onward attacks that appear to originate from the organization hosting the vulnerable application, leading to potential legal liabilities and reputational damage._

.For more info about SSRF vulnerabilities check this article from [portswigger.](https://portswigger.net/web-security/ssrf)

**Final Conclusion:** I chained both vulnerabilities to get to one point which’s accessing the Facebook internal network ([SSRF](https://portswigger.net/web-security/ssrf)), By using the Account takeover to reach into my uploaded script inside the application which will send custom-crafted requests as I want.

Let’s talk about what I’m able to achieve by using the chain of vulnerabilities I found until now:

-   **I’m able to access any Facebook employee account in the legal department panel.**
-   **I don’t need to explain the kind of juicy information the attacker could find after signing in.**
-   **I was able to use the SSRF to access the Facebook internal network (intern.our.facebook.com).**
-   **With a little more effort I believe I was able to escalate this vulnerability and use it to scan the internal network/servers.**

we all know how critical the [**SSRF**](https://portswigger.net/web-security/ssrf) is especially it’s not rate limited and I can easily edit the [**content type**](https://www.geeksforgeeks.org/http-headers-content-type/) and the request methods and as the Facebook [**payout guidelines**](https://www.facebook.com/whitehat/payout_guidelines/ssrf) say this vulnerability should be rewarded with a bounty of **$40,000** with **$5000** bonus if I’m able to edit the request [**content type**](https://www.geeksforgeeks.org/http-headers-content-type/) and the [**request method**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

After a long time of waiting, I just got this message from Facebook :

![](https://miro.medium.com/max/1392/0*cHNrPi_4354gv3K5.png)

Got rewarded by Facebook **$40,000** plus a **$2,000** bonus (which should be **$7,000** lol)

so I asked them why I did not get the full control bonus (**$5000**), and the answer was:

![](https://miro.medium.com/max/1390/0*WnbCuQSjDY6cw6p-.png)

![](https://miro.medium.com/max/1390/0*Fd2hp0tvXzmm0OGa.png)

The total with the first vulnerability is **$54,800**

I reported this vulnerability days after the vulnerability in part one.

Report time Line:

-   **Wednesday, September 9, 2020 — Vulnerabilities reported.**
-   **Monday, October 26, 2020** — **Facebook asked me to open a new report. ~Mitigation applied.**
-   **Monday, October 26, 2020 — Report Triaged.**
-   **Thursday, February 25, 2021 — Fixed and rewarded.** ~ **_6 months I know lol._**
-   **Friday, March 5, 2021 — Bonus $5300 rewarded.**

I would like to give a **Golden** tip for the bug hunters, Always when you see **ASPXAUTH** try to get the cookies from another website that using the same application and test the same method that I did:

-   Create new **ASPXAUTH** cookies from the other website.
-   Test if the cookies will work on your target website.

I enjoyed this but waiting 6 months and closing reports for irrational reasons I’m very grateful but all this hard-working, and this is not the only SSRF I found, actually, I found more interesting ones but Facebook closed them as informative because Facebook signed an agreement with the vendor which it was signed weeks after the reports got Triaged which this is not my problem actually, so anyways I just won’t call it the best experience.

This is the final part and I’m sorry if anything is not clear and for the delay in providing part two, as mentioned before, I waited some time for written permission, and the report got revised, so a lot of things got removed or blurred to keep the privacy of the other parties.

you can follow me on Twitter For more write-ups and tips: [https://twitter.com/alaa0x2](https://twitter.com/alaa0x2)

and you can check the write-up on my personal blog:

[https://alaa.blog/2021/02/how-i-hacked-facebook-part-two/](https://alaa.blog/2021/02/how-i-hacked-facebook-part-two/)

**Cheers.**

