# Dockware Essentials

With dockware \#dev you always see the preinstalled Shopware version in your docker-compose.yml \(dockware/dev:6.2.0, ..\).

However, if you have longer running projects, especially when developing for a client, and you have "more" than just a plugin, you might handle Shopware and all additional updates on your own.

This means that the version of your compose file, can actually differ from the one that is currently used.

In this case dockware \#essentials can help you :\)

This image does not contain any Shopware version, but still offers you a complete Shopware 6 environment. Just upload any of your projects in any Shopware 6 version.

It's also a perfect fit if you decide to migrate to dockware.

**How to use**

We recommend starting with our \#dev guidelines and then use \#essentials.  
  
It's basically the same as in our \#dev workflow, only without the "downloading" process to your host.  
Using \#essentials is more about "uploading" your shop from the host to your container.  
  
You can also use all tools like XDebug, watchers, mailcatcher, and also additional features of Docker and more.  
\(Please keep in mind that you might need to modify some commands in your own project to make them work with the integrated dockware services\).  
  
**Essentials Image:**

```text
dockware/essentials:latest
```

