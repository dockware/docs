# MySQL Users

You can easily switch the credentials for the installed MySQL instance.\
If you do this, the original root user is being blocked for new remote connections and a new user with the provided credentials is being created for you.\
\
You can also change the password whenever you want as long as the original root user password stays the same.\
\
\
Please see our section about [Environment Variables](../development/environment-variables.md) for more details about the available configuration settings.

```yaml
dockware:
      image: dockware/dev:latest
      ...
      environment:
         - MYSQL_USER=userABC
         - MYSQL_PWD=supersecret
```

{% hint style="danger" %}
Attention! Please note, this will only work if you have not touched the original MySQL root user password. Also, we recommend database backups before changing any credentials.
{% endhint %}
