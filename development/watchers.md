# Watchers

If you are either working on the storefront or the administration you might want to use the watchers for it.  
These will make sure to compile everything as soon as new changes and file modifications have been recognized.  
You only need to reload your browser and all changes are visible immediately.  
  
Because the watchers are not very dev-friendly sometimes, we've prepared a small little makefile for you.  
It's outside the DocRoot in the directory **/var/www**

```text
cd /var/www
make
```

```bash
watch-storefront               #Starts watcher for storefront at http://localhost
stop-watch-storefront          #Reverts everything back to normal operation
        
watch-admin                    #Starts watcher for admin at http://localhost:8888
```

### **Administration Watcher**

If you want to start the watcher for the administration, run the corresponding makefile command.  
This takes a bit, but not that long.  
Afterwards please open the administration with this URL:  
  
[http://localhost:8888](http://localhost:8888/)  
  
As soon as you modify any files in the administration and upload them to the container, the watcher will recognize the changes, and compile the app again. In addition to this, it will also automatically refresh your browser \(Hot Module Replacement\).  
  
To stop the watcher, simply cancel the command / process.



### **Storefront Watcher**

The watcher for the storefront requires a few things to be set.  
Follow this simple guide to get started.

1. **Ports** You need to expose the correct ports of your Docker container. Please expose the ports "**9999**" and "**9998**". The easiest way is to just expose them directly with "9999:9999" and "9998:9998" in your Docker setup. 
2. **Start Make Command** Connect into your container and navigate to the folder "/var/www". This prepared makefile contains a command to start the storefront watcher. 
3. **Open Browser** If the make command has finished, your watcher should be ready. Just open the URL in your browser and you should see that the CSS styles are loaded in a lazy way. That means it is working. Please keep in mind, that different Shopware versions require different URLs to be used. Shopware &lt; 6.4: [http://localhost](http://localhost) Shopware &gt;= 6.4: [http://localhost:9998](http://localhost:9998)

The storefront watcher requires a special Request Header to be set.  
We know this is annoying, so we've injected it automatically for you when starting the watcher using our make command.

This command creates a new .htaccess file that automatically sets the required header.  
So there's **no need to do anything on your side**! Just code!  
  
Please consider, due to this, there's a separate "stop" command in the make file to remove it again and get back to the original storefront without the watcher.  


