# Custom Domains

We have prepared everything for a smooth launch using http://localhost!  
But it's of course possible to use your custom domain.

  
There are only 3 things to configure:

1. **Change Shopware Domain** Configure your custom domain either in the Shopware administration \(Sales Channel\) or directly in your database. Make sure to enter the correct HTTP protocol along with your custom domain, such as "http://dockware-local.com".... You can also use a custom port in here by appending it to your domain like in this example "http://localhost:1000". \(please note that this port needs to be exposed in Docker!\) 
2. **Clear Caches** Make sure to clear the caches of your Shopware shop. Otherwise, you will get an error in the storefront. 
3. **Configure Domain on Host** We need to tell your host that requests to this new domain are being forward to your localhost. This can be by modifying the local `/etc/hosts` file. Just open it and add your new domain along with the localhost IP address like here:

{% code title="/etc/hosts" %}
```text
127.0.0.1        dockware-local.com
```
{% endcode %}



{% hint style="success" %}
Congratulations!  
Your Shopware shop is now accessible under your custom domain!
{% endhint %}



### Proxies, Load Balancers and Ports

If you use any kind of proxy before the container, or if you use a custom port, please note that you might need to adjust a few things here.

So your proxies obviously need to listen to that domain too and forward it to the Shopware container.

Also, if you use a custom port, please make sure that this port is also accessible by exposing it within your Docker infrastructure like in this example

```text
image: dockware/play:latest
ports:
    - "80:80"
    - "1000:1000" # new 1000 port that is used in sales channel
```





