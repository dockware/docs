# Security

Security is always important!  
Please keep in mind, that dockware is primary made for local development!  
  
This does not mean you cannot use it for a server that is available on the internet.  
But please consider a few security related things.

**Don't expose ports like you would do locally**

In our docker-compose.yaml samples, you see all kinds of ports being exposed.  
This is great for local development - but not for a server - even it's "just" a staging system.  
  
Our recommendation is to only expose ports that are really necessary to use your app.  
And this should only be done through 1 single docker container, probably a proxy like NGINX.  
This helps you to avoid losing control over what is exposed throughout your \(bigger\) yaml file.  
  
If you expose a port, make sure to add an additional restriction if possible.  
  
This sample would only expose the port 3306 MySQL for connections from the localhost \(host system\).  
So you can do a default SSH connection to your host, and then a connection from there to your MySQL container. This is pretty much the basic workflow of such a scenario - only with Docker ;\).

{% code title="docker-compose.yaml \(partial\)" %}
```text
127.0.0.1:3306:3306
```
{% endcode %}

{% hint style="danger" %}
**Attention**

Please do never expose port 22 from dockware on an online server if you do not know what you are doing! Pay attention to the weak default credentials - and consider using a firewall.
{% endhint %}

