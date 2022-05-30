# Use Https/SSL

Dockware images come already with installed self-signed certificates.\
You can even overwrite those certificates and install your certificates with Bind-Mounting (or any other way).

These are the used default locations:

```bash
SSLCertificateFile /etc/apache2/ssl/server.crt
SSLCertificateKeyFile /etc/apache2/ssl/server.key
```

Using HTTPS locally or on a hosted machine is very easy.\
Just make sure to **expose port 443** in your Docker container.

Afterwards it should already work using _https://localhost_ for example.

{% hint style="info" %}
If your Chrome warns you about the self signed certificate, just continue to the website or simply type **thisisunsafe** as a magic word.
{% endhint %}

If you want to use a different domain instead of https://localhost, you only need to login to the Shopware Administration and configure the domain in your sales channel. (maybe also clear your caches).

Then you only need to tell your host that this domain exists and that it should just map to your localhost IP address. For this, edit your local _/etc/hosts_ file and add a new entry like below. This only needs to be done once on your machine.

```bash
127.0.0.1    local.shop.com
```

{% hint style="info" %}
Please avoid domains like **\*.dev** and **\*.local**, because these usually bring some troubles with them. They have indeed special handlings in some browsers.
{% endhint %}
