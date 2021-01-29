# Update Dockware



We work hard on maintaining dockware.  
So there might be updates from time to time to existing images.  
  
These updates will not get a new tag version, but stick with the tagged Shopware version.  
Run this command to test for new updates and automatically download it.ON HOST

```text
docker pull dockware/play:latest
```

Run this command to update and download all versions of this image. Please note that this consumes more disk space.

```text
docker pull dockware/play -a
```

{% hint style="warning" %}
Attention! Please keep in mind, if you use any type of volume for your Shopware source code - the source code will not be updated! Only the container is being updated locally on your host.
{% endhint %}

