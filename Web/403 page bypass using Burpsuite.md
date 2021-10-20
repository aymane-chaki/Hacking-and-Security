

[![Amine Aboud](https://miro.medium.com/fit/c/56/56/1*n4fAe7rS-bkJVXBqPmsJiQ.jpeg)](https://amineaboud.medium.com/?source=post_page-----fc0875eb5125--------------------------------)

Hello world!

I have always been interested in the challenge of testing the security of a company like Facebook. With over 2.7 billion monthly active users, it is the biggest social network in the world.

That being said, here’s a quick write up on a distinct vulnerability I found and reported recently to Facebook.

![](https://miro.medium.com/max/2506/1*-axlmhaXphnqhXNt1XiOwA.png)

While doing some subdomains enumeration, I found a subdomain which instantly raised my interest : [_https://legal.tapprd.thefacebook.com/_](https://amineaboud.medium.com/%20legal.tapprd.thefacebook.com/)

The reason being that servers used for “legal needs” usually contain important data.

I started googling afterwards and found the following endpoint indexed on the search results [_https://legal.tapprd.thefacebook.com/tapprd/auth/identity/logout_](https://legal.tapprd.thefacebook.com/tapprd/auth/identity/logout)

& so the hunt began. 👨💻

![](https://miro.medium.com/max/60/1*KThoiC7jksgg4ZZYHSv0_A.png?q=20)

![](https://miro.medium.com/max/3128/1*KThoiC7jksgg4ZZYHSv0_A.png)

While doing some directories enumeration, a strange server behavior caught my attention. I noticed that when I tried to request some specific directories, the server’s response was delayed by a few seconds before returning the error : “**403 Forbidden : Access is Denied.**”

![](https://miro.medium.com/max/60/1*hXEaz_kYEDcVL50RszCErw.png?q=20)

![](https://miro.medium.com/max/3412/1*hXEaz_kYEDcVL50RszCErw.png)

I decided to send 200 http requests (without any payload) targeting [_http:// legal.tapprd.thefacebook.com/tapprd/_](http://legal.tapprd.thefacebook.com/tapprd/) to stress the server up a little and watch its reaction.

Without expectation, I sent requests with Blurp Intruder using the following options :

Number of threats: 6

Numbers of retries on network failure: 4

Pause before retry (milliseconds): 3000

and… i left my computer for a cold beer. 🍺

Little did i know that 30 minutes later i’d be impressed by the results!

By sending multiple simultaneous HTTP requests to _/tapprd_/, some requests managed to bypass the 403 permission denied error and got a full directory listing. 🤩

After digging further and doing some additional tests, I came with the following conclusion: sending simultaneous HTTP requests to a specific directory can lead to the server leaking its content.

I sent HTTP requests via Blurp (again) and at the same time I opened [http://legal.tapprd.thefacebook.com/tapprd/](http://legal.tapprd.thefacebook.com/tapprd/) with Firefox. The 403 error disappeared and I got a beautiful open directory listing:

![](https://miro.medium.com/max/60/1*q88I_Ixrrz0eQNBkncR1jg.png?q=20)

![](https://miro.medium.com/max/2520/1*q88I_Ixrrz0eQNBkncR1jg.png)

I started navigating through the folders with Firefox and I found an upload directory with some strange XLSX files:

![](https://miro.medium.com/max/56/1*5qrqofGXuMrYIzXIVHrZ6A.png?q=20)

![](https://miro.medium.com/max/2358/1*5qrqofGXuMrYIzXIVHrZ6A.png)

I clicked to check few samples and… BOOM!💥 These documents were uploaded by the legal Facebook team and were containing a lot of internal confidential business and personal informations. I decided to stop my research, prepared a POC video and sent a detailed vulnerability report to Facebook.

**July 27, 2020 at 3:43 PM :** The vulnerability has been reported and just one hour later, the report was triaged by the Facebook Security Team  
**July 31, 2020:** The vulnerability has been fixed by the Facebook product team and I confirmed the patch.  
**August 12, 2020:** Facebook raised a conflict with the Responsible Disclosure Policy — I sent more clarifications and details.  
**August 27, 2020:** Got a reply « We have received your comments and will get back to you on this matter once we have finished discussing the case. »  
**September 21, 2020:** Finally: « Upon further review of your report, we’ve decided to issue you a bounty award. We appreciate your patience as we worked through the triage process. »  
**September 28, 2020:** Bounty awarded. 💰

![](https://miro.medium.com/max/2084/1*ZnughW4wQObpv9BVDhmEwA.png)

1.  Be attentive to small details
2.  Think outside the box
3.  A beer is always a great idea 🍺
4.  Once you discover a vulnerability, control your emotions and steps. Make sure to always carefully respect the Disclosure Policy of the Bug Bounty Program you are participating to.
5.  Even if it’s hard and frustrating, be patient… This report took 2 months before getting completely resolved.

Cheers!  
Amine Aboud

Twitter: [https://twitter.com/amineaboud](https://twitter.com/amineaboud)