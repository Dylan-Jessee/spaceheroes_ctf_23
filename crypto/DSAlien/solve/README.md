# DSAlien
Author: **Pwnut**
<br><br>
I thoroughly enjoyed creating this challenge, a really fun learning experience for me jsut as I hope it was for you.
### The Premise
So Al starts talking to us, but of course so that he can make sure it's really you talking to him he needs you to sign your messages with DSA. He also points out that you could potentially send a command to his ship, but it would only
work if he were the one who sent it. You can see in the code that if the message `COMMAND: TAKE CONTROL` was signed by Al's DSA key you would get the flag.
### Where To Start
You are able to add your own key, but he gives you the ability to generate your own DSA key which makes starting the conversation a little easier. Now that you have your DSA key, you can start sending him messages. What's strange though
is the fact that you can set the upper limit for the random generation of your own k for signing your message. You can read up on it [here](https://rdist.root.org/2010/11/19/dsa-requirements-for-random-k-value/), but long story short,
k must be "unpredicatable and secret" in order to protect the signer's private key. So, if we can predict k for Al's next message to us, then we can calculate his secret key using that and the public DSA info he provided us earlier. k
must also be some number between 2 and q, which begs the question as to why the the lower limit to the randrange was 0.
### In Comes Randcrack!
A really interesting python module that, with enough input from python `random`, can predict next numbers to be randomly generated. Check it out [here](https://github.com/tna0y/Python-random-module-cracker)!
<br>
Fortunately for us, Al was relying on python's random module to generate random k's. On top of that, he gives you a lot of control over how random is generated. If you look at the randcrack module, you can see that 624 32 bytes from
random would be enought to start predicting the next numbers to come, and since we have control over the upper limit for random.randrange(0, k), we can submit 4294967294 and feed the output to randcrack! Something I'd like to point out
here is the fact that though the upper limit to k can be a very small number (e.g.-3), it might fail in it's generation since the likelines of the random number being 0, 1, or 2 is very high. It was necessary for randcrack to function
however.
### Calculating Al's Private Key & Profit
After we set feed randcrack the required info, we can calculate the very next random number to be used. If you send a message to Al, he'll respond, meaning we can predict his k generated by "random.randrange(0, key.q)"! Read here
(how)[https://rdist.root.org/2009/05/17/the-debian-pgp-disaster-that-almost-was/] on how to calculate a DSA key from the k, r, s, and h of a message along with the public components of the DSA key. After it's calculated, you can set your
key to be Al's, send the command, and get the flag!