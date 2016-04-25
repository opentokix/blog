---
layout: post
title: Blogging as a sysadmin
category: python 
tags: blog, sysadmin, linux, debian, ubuntu
---
This is the first post, and I was thinking I would note down some stuff I did in python today.
Trying python3 print function for fun and profit. 

To use it in python2: 
{% highlight python %}
#!/usr/bin/env python2
from __future__ import print_function
print("Starting: ", end='')
for i in range(10):
        print(i, end='')
            print("Done!")
{% endhighlight %}

And it is native in python3 

{% highlight python %}
#!/usr/bin/env python3
print("Starting: ", end='')
for i in range(10):
        print(i, end='')
            print("Done!")

{% endhighlight %}
This will print: 

Starting: 0123456789Done!

And that is super exciting! 

My perfered Linux distribution is 

![Debian logo](https://random.0x7b8.net/pics/Debian_logo.png)
