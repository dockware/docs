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

**Administration Watcher**

If you want to start the watcher for the administration, run the corresponding makefile command.  
This takes a bit, but not that long.  
Afterwards please open the administration with this URL:  
  
[http://localhost:8888](http://localhost:8888/)  
  
As soon as you modify any files in the administration and upload them to the container, the watcher will recognize the changes, and compile the app again. In addition to this, it will also automatically refresh your browser \(Hot Module Replacement\).  
  
To stop the watcher, simply cancel the command / process.

**Storefront Watcher**

If you want to work on the storefront, please start the watcher command for this.  
The storefront is available with this URL:  
  
Shopware &lt; 6.4: [http://localhost](http://localhost)  
Shopware &gt;= 6.4: [http://localhost:9998](http://localhost:9998)

  
In case you are wondering why no additional port 9999/9998 is being used, the answer is pretty simple.  
If you take a close look at the XHR request, you'll see that only the assets like JavaScript files are being loaded from that port.  
  
One of the things in Shopware 6 that makes using the storefront watcher a bit harder, is that the trigger if port 9999 should be used or not, is done by using a request header entry.  
  
Most developers out there tend to just change that variable in the twig file.  
  
If you use our make commands, a new .htaccess file is being injected which automatically inserts that header for you.  
So there's **no need to do anything on your side**! Just code!  
  
Please consider, due to this, there's a separate "stop" command, to remove it again and get back to the original storefront without the watcher.  


