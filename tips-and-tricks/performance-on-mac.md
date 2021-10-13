# Performance on Mac

**Docker is slow on your MAC?**

This can have many reasons!\
It's good to have a basic knowledge in Docker and also to cleanup your system.\
We want to give you some insight in our best practices which work really good for us.

**Don't use Bind-Mounting!**

One of the most mistaken things out there when people say Docker for MAC is slow,\
is because they use bind-mounting with a huge number of files (assets, caches, and more).\
And yes, Shopware and also Symfony have lots of these files.\
In Linux it works better, but shouldn't we just make a step back and focus on what the problem is?!\
\
Docker offers us a way of running a separate and isolated system on our host machine.\
In combination with lots of deeper integrations such as "docker cp", "docker exec", "bind-mounting" and more, we tend to think we really need these things. And that's the problem!\
\
Our best practice is to use SSH/SFTP and **strictly avoid any bind-mounting** and anything that shares files between your host and your container!

**But what about coding and all these developer tools?!**

No worries. It always worked that way in earlier days.\
Write code on your host, and use SFTP automatic uploads.\
\
If you have any watchers - just run it in your container and as soon as you upload anything using SFTP, they will start to recognize changes and give you the expected output.\
\
Simple and easy, isn't it?!

**But hey, my initial upload with SFTP just takes too much time!**

Yes, that's correct!\
For initial uploads we do indeed use the "docker cp" command. That's a lot faster.\
So just use this command, maybe do some "chown" permission changes and then go back to SFTP upload.\
\
Here's an example:

{% code title="ON HOST" %}
```
docker cp src/. my-container:/var/www/html
docker exec my-container bash -c 'chown www-data:www-data /var/www -R'
```
{% endcode %}

**Docker is slow again after lunch break**

You might experience performance problems after your MAC has been in sleep mode for a while.\
This is usually if you come back from lunch or from long meetings.\
\
If it's disturbingly slow, simply restart Docker and start dockware again.\
That's the easiest thing.\
\
You can also prevent your host from sleeping or from turning off its disks in such cases.
