## The best way to understand a technical is finding some documents.

For understanding this two bits SN&NESN, I find that this artical(below link) has a good opinion.
This guy say： 
1. NESN and SN are looked at independently.
2. When the device sends a packet, the NESN is the next expected SN from the other side in the next packet coming back.
https://novelbits.io/understanding-sn-nesn-ble-link-layer-packet/#:~:text=A%20few%20notes%20to%20better%20understand%20these%20bits%3A,side%20in%20the%20next%20packet%20coming%20back.%20%E6%9B%B4%E5%A4%9A%E9%A1%B9%E7%9B%AE

Here is a good artical writing by Chinese.
https://www.cnblogs.com/iini/p/8977806.html



SN means Sequence Number, and NESN means NExt Sequence Number.
We can take it literally, SN is used to give index to an air packet, so for me, firstly I want to use Arabic numerals to code them, such as 
SN 1   ##packet
SN 2   ##packet
SN 3   ##packet
SN 4   ##packet
SN 5   ##packet
SN 6   ##packet
SN 7   ##packet
and then, we have a question that in our LL header packet, we have one bit to store this SN(Sequence Number),
so to solve this question, we need to overflow SN, such as
SN 1   ##packet
SN 0   ##packet
SN 1   ##packet
SN 0   ##packet
SN 1   ##packet
SN 0   ##packet
SN 1   ##packet
That's good, we solve the question which storage limitation gives us.

Next, we need to understand NESN, we also take it literally, NESN means telling the person who are talking with you I need you send 
 to me SN that equals to NESN I sent to you. In my brain, I think if this guy don't send me SN that I needed, that means this guy don't 
recieve my packet, so I need to send old packet again. 

So it's my view of understanding this data flow control protocol. It's easy to understand if I seperate these two parameter.