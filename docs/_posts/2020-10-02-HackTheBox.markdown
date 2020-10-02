---
layout: post
author: Unsociable
title:  "Hack The Box"
date:   2020-10-02 21:07:18 +0100
categories: PenTesting
---

Gaining an Invitation Code for HackTheBox
======

This is going to be my first write up, any feedback is greatly appreciated! 

So first off, we are going to be looking at getting an access code for HackTheBox, a Pen-Testing Lab site.

[HackTheBox](https://www.hackthebox.eu/)

Registration Page
------

Upon opening the [registration page](https://www.hackthebox.eu/invite) it's asking us to `Hack this page to get your invite code!`

Step One
---------

The first thing I am going to look at is the Inspector Tool inside Chrome. Every modern browser has this feature. In Chrome I use the following keystrokes `Ctrl + Shift + I`

Step Two
---------

![](https://i.imgur.com/mkPPIiT.png)

As you can see in the above image, it's telling us that the page loads an interesting Javascript file. To see the loaded files, we are able to go to the Sources tab at the top.

Step Three
---------
![](https://i.imgur.com/mg6JTD9.png)

Here, we can see the inviteapi.min.js has the following code

    //This javascript code looks strange...is it obfuscated???

    eval(function(p,a,c,k,e,r){e=function(c){return c.toString(a)};if(!''.replace(/^/,String)){while(c--)r[e(c)]=k[c]||e(c);k=[function(e){return r[e]}];e=function(){return'\\w+'};c=1};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p}('0 3(){$.4({5:"6",7:"8",9:\'/b/c/d/e/f\',g:0(a){1.2(a)},h:0(a){1.2(a)}})}',18,18,'function|console|log|makeInviteCode|ajax|type|POST|dataType|json|url||api|invite|how|to|generate|success|error'.split('|'),0,{}))
Step Four
------
Using the [Cyber Chef](https://gchq.github.io/CyberChef/) website, we are able to to Beautify the statement.

    eval(function (p, a, c, k, e, r) {
    	e = function (c) {
    		return c.toString(a);
    	};
    	if (!''.replace(/^/, String)) {
    		while (c--)
    			r[e(c)] = k[c] || e(c);
    		k = [function (e) {
    				return r[e];
    			}];
    		e = function () {
    			return '\\w+';
    		};
    		c = 1;
    	}
    	;
    	while (c--)
    		if (k[c])
    			p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]);
    	return p;
    }('0 3(){$.4({5:"6",7:"8",9:\'/b/c/d/e/f\',g:0(a){1.2(a)},h:0(a){1.2(a)}})}', 18, 18, 'function|console|log|makeInviteCode|ajax|type|POST|dataType|json|url||api|invite|how|to|generate|success|error'.split('|'), 0, {}));

Here, we can see an function showing makeInviteCode, along with a POST event. Lets try the function.

Step Five
----
Inside Chrome, we type makeInviteCode in the Console Window.

![](https://i.imgur.com/9OW1oXM.png)

Here, we can see it replies with the POST statement. Using curl, we are able to send information to this POST. As I am in Windows, I am going to do this with Invoke-WebRequest.

Step Six
-----

In the statement above, we can see the url is `/api/invite/how/to/generate` so we will send this `Invoke-WebRequest -uri "https://www.hackthebox.eu/api/invite/how/to/generate" -Method POST -Body []`

Step Seven
-----
We have our response back from the API. 
![](https://i.imgur.com/AUSB7kP.png)

Here, we can see that the enctype or encryption type is ROT13. The data is `Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb \/ncv\/vaivgr\/trarengr`

Going back to [Cyber Chef](https://gchq.github.io/CyberChef/) we are able to decrypt this using the ROT13 recipe.

![](https://i.imgur.com/kxc5W0L.png)

Step Eight
------

Now, the result from our encryption is telling us to make a second POST request to `/api/invite/generate`

Using the same method as Step Five, we are going to Invoke-WebRequest. We're going to assume the data type is JSON again and send a blank body.

`Invoke-WebRequest -uri "https://www.hackthebox.eu/api/invite/generate" -Method POST -Body []`

Step Nine
-----

![](https://i.imgur.com/sSRb2XZ.png)

The result of our POST request is a success. The code there is stated as being encoded. 

Using the [Cyber Chef](https://gchq.github.io/CyberChef/) site we are able to decrypt this.

I'm not going to reveal the operation used, however doing this will give you the Invite Code.

Enjoy & Happy Hacking!