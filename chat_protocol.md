# Laboratory work 6 - tasks implementation

**Algorithm of work:**
1. Wireshark application has been stared;
2. Open 2 instances of Application: Corina and Corina-Instance-2;
3. As there are more than 1 application running on the Computer, which make use of Internet, in the wireshark are displayed multiple transmitted packages(img1.png) ![alt text](imgs/intf.png).
**4.Applicatoin Package indetification** <br/>
In order to filter the packages the data was filtered according to `udp.port == 42424`, as the application is running in UDP, port-42424 ![alt text](imgs/hex.png);

**5. Analyzis of received/send data**<br/>
Studying a little bit the Chat-Application, had been identified 2 types of 'actions': Login or Connecting to the Chat, and Sending a message, that's why we will have to study 2 types/structures of packages.

**6. Structure 1: Login(Connecting) to the Chat package**<br/>
Package structure is presented in figure ![alt text](imgs/intf.png)..
This is the sent package content. On the left side we have hexa-decimal representation of the package content, on the right we have the data in Ascii representation. In fact this is the transmitted data encoded in Base64.
-The ASCII 'text' had been coppied, and converted through a Hex-to-String converter.<br/> 
Coppied data:
[01005e39c06c3010b3be5ee10800450000a8250640000111eb62c0a8010ee6b9c06cd581a5b800944e624d5455794e6a55774d7a67304e7a45354d6e77355a574e6d4f5445315a53307a5a4463354c5452685a6d4d7459545a6a595330334e4745335a6d51345a44673559545a384f6d46736248786c656e41775a56684362456c4563485a6962586877596d315663306c456344466a4d6c5a35596d31476446705451576c524d6a6c355956633161456c754d44303d]
<br/>
Converted: 
[MTUyNjkxNTg2OTUyM3w1ODNkMDI2NS05Yjc1LTQ0YTMtYWY2Ny1mMjkxMjFmNjlkNTR8OmFsbHxlenAwZVhCbElEcHZibXhwYm1Vc0lEcDFjMlZ5Ym1GdFpTQWljMmhoWkc5M0luMD0=]
<br/>
Base64 Decoded:
[]nS[1526915869523|583d0265-9b75-44a3-af67-f29121f69d54|:all|ezp0eXBlIDpvbmxpbmUsIDp1c2VybmFtZSAic2hhZG93In0=]
<br/>
6.1. `1526915869523`-  Time from miliseconds: Mon May 21 2018 15:17:49<br/>
6.2. `583d0265-9b75-44a3-af67-f29121f69d54` - UUID assigned to the logged user. This UUID will be used later so it is important to understand what it is for.<br/>
6.3. `:all` - This field is a filter to tell the program that the packet he received needs to be broadcasted to all the users connected to the application-server.<br/>
6.4. `ezp0eXBlIDpvbmxpbmUsIDp1c2VybmFtZSAic2hhZG93In0=` - this is another Base64 encoded string.<br/>
Decoded value: `{:type :online, :username "shadow"}`.<br/>



**7. Sending a package, another user connects to the application.**<br/>
7.1. Initial value - `{:type :online, :username "shadow"}` <br/>
Base64 Encoded - `ezp0eXBlIDpvbmxpbmUsIDp1c2VybmFtZSAic2hhZG93In0=`
7.2. Based on the studied package structure, has been created a new package.<br/>
-Current time has been generated to milliseconds.<br/>
-the previous UUID has been modified as UUID must be Unique for each user.<br/>
New Package Content-[1526917412911|68b6a7ff-f1fd-441c-b71c-e0a38d3142da|:all|ezp0eXBlIDpvbmxpbmUsIDp1c2VybmFtZSAic2hhZG93In0=]<br/>
7.3. Encode to Base64 the package content - [MTUyNjkxNzQxMjkxMXw2OGI2YTdmZi1mMWZkLTQ0MWMtYjcxYy1lMGEzOGQzMTQyZGF8OmFsbHxlenAwZVhCbElEcHZibXhwYm1Vc0lEcDFjMlZ5Ym1GdFpTQWljMmhoWkc5M0luMD0=]<br/>
7.4. The package has been sent using PacketSender app. Data like: IP and Port had been retrieved from wireshark.<br/>
7.5. The Tricky user had appeared in the chat. Moreover 3 new packages had appeared in the wireskark. Why 3 (Len=140 ,Len=184, Len=188)? (1- the message of connection to the server, 2- two broadcasted messages to the users from the chat: andrei and andrei-2, In order to announce them that there is a new user in the chat!) ![alt text](imgs/chat.png).<br/>




**8. Structure 2: Message sending package**<br/>
Once a message is sent in the chat, has been observed that 2 new packages appeared in the wireshark.<br/>
8.1. Package with the message that was sent<br/>
Decode Value - [1526916248926|583d0265-9b75-44a3-af67-f29121f69d54|efc1aec7-9a8a-44d7-b4f2-96eaecf026c3|ezp0eXBlIDpjaGF0LCA6dHh0ICJzYXkgaGVsbG8ifQ==]<br/>
Content - [{:type :chat, :txt "say hello"}]<br/>

8.2.Package with the aknowledgement message that was sent<br/>
Decode Value - [1526916248929|efc1aec7-9a8a-44d7-b4f2-96eaecf026c3|583d0265-9b75-44a3-af67-f29121f69d54|ezp0eXBlIDpkZWxpdmVyZWR9]<br/>
Content - [{:type :delivered}].








