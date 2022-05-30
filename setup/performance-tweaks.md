# Performance Tweaks

To improve the performance of your overall docker system, you might want to tweak a few things. We do this by opening our docker preferences on our host machine.

Please note, these are no must-haves, but still help give you a better overall experience of your docker system.

Linux based users need to configure it using the bash, if even needed for their systems.

### **Resources**

We start by configuring the amount of CPUs and Memory Docker will get from your host system. This obviously depends on your system and its resources.

When changing these numbers, please keep in mind that the more resources you give Docker, the less your host system has.

We had the best experience by using half of the CPUs (3-4) and about 6-8 GBM RAM for Docker.

![dockware ressources - cpu and ram options](../.gitbook/assets/docker-ressources-settings.jpg)

### **Resource File Sharing**

This section tells Docker what folders are ready to be used with bind-mounting. As you might see after installing Docker, the whole system is available.

That means, when docker starts, it will prepare the whole system to be used for bind-mounting, which is of course an overload that can be reduced.

We recommend only adding your projects root folder and for some special use cases your \~/.ssh folder.

![dockware ressources - file sharing options](../.gitbook/assets/docker-filesharing-settings.jpg)

### **Storage Driver**

Docker works with different storage drivers. The fastest one at the moment is "overlay-2".

To enable this as global storage driver, just add the following entry to your Docker Engine settings.

```
"storage-driver" : "overlay2"
```

![dockware resources - storage driver](../.gitbook/assets/docker-resource-storage.png)

