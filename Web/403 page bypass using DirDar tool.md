[![Lavan Kumar](https://miro.medium.com/fit/c/56/56/1*vZC73nYAHOxqERs7g9QNKg.gif)](https://uduthalavankumar.medium.com/?source=post_page-----ee16c434add7--------------------------------)

Welcome to my knowledge hub spot and Hope you all are doing Good. During my hunting time, I have seen many URL’s which is showing 403 Forbidden response. I thought why shouldn’t I try to bypass once and let’s see where it goes. I referred some hackerone reports regarding the same vulnerability.

I started hunting on private RVDP sites and I found 403 Forbidden page on one of the reputed private Ivy League research university in **Cambridge**, Massachusetts.

Let’s get into the post without further delay!

![](https://miro.medium.com/max/1800/1*E34r4LWmwv8fkEpxcfvAjQ.jpeg)

## 403 Forbidden

The HTTP `**403 Forbidden**` client error status response code indicates that the server understood the request but refuses to authorize it.

The simple reason why we are seeing this **error** is that you are trying to access something you don’t have the permission for. Throwing a **403 forbidden error** is your website’s way of stating that you don’t have enough permissions to proceed further. This **error** is basically due to: Incorrect file or folder permissions.

To find 403 forbidden pages, we can use github tool that will help you bypass the forbidden directories called “**DirDar**“.

Let’s take a look**!!**

## Golang Installation

This tool is compatible with all kind of operating systems but you should have GO compiler installed.

> **apt install golang -y**

![](https://miro.medium.com/max/1212/1*uLly1O1iHkUM19D-wsFupg.png)

Once the Go compiler is installed and configured, execute the following commands.

> **go get -u github.com/m4dm0e/dirdar**

![](https://miro.medium.com/max/954/1*o9yvYnR-hGAJLndkT2EJcg.png)

**Done!!** Now you can use the following command to boot this tool.

> **dirdar -h**

![](https://miro.medium.com/max/1140/1*0J2AcBEejkREYr68GlCEsA.png)

## Bypassing Forbidden Directories

Bypassing 403 Forbidden Errors indicates that the client was able to communicate with the server, but the server won’t let the client access what was requested. Although the tool uses several combinations to resolve this error. Let us give you an example. As you can see we have found a location that the client is unable to access.

> **dirdar -single** [**https://**](https://secnhack.in/)**redacted.com -wl word.txt**

![](https://miro.medium.com/max/1540/1*scw-gzSpMpNQ5dtpXmoeKA.png)

Note: Give the fuzz-Bo0oM.txt from SecLists in place of word.txt

You can download SecLists from below link.

Even you can see that once we open the location, we get a **403 error** which shows that the server does not want to give access.

![](https://miro.medium.com/max/2732/1*FtalRgxlffPmyZvFyUwJ2g.png)

403 Forbidden Page

Now, Inorder to bypass this page give the following command in Kali

> **curl -i -s -k -X $’GET’ -H $’Host: redacted.com’ -H $’X-rewrite-url: /!.htaccess/’ $’**[**https://redacted.com'**](https://huit.harvard.edu%27/) **-o output.html**

Now, open the saved output html file.

**Boom!** You can access the webpage now. 403 Forbidden is bypassed!!

![](https://miro.medium.com/max/2714/1*Vmr3AbF-qiaJqjZxPF4oPg.png)

403 Forbidden bypass page

I hope you all learned something new today!

Source Imgaes : **Secnhack, Thank you!**

**_Note: Don’t forget to give credit to me if you find any bug/bounty with the help of this post._**

Stay Safe and Stay Connected!!

1.  [LinkedIn](https://www.linkedin.com/in/lavan-kumar-71001a74/)

2\. [Twitter](https://twitter.com/cyberdefender5)