# Watchers

If you are either working on the storefront or the administration you might want to use the watchers for it.\
These will make sure to compile everything as soon as new changes and file modifications have been recognized.\
You only need to reload your browser and all changes are visible immediately.\
\
Because the watchers are not very dev-friendly sometimes, we've prepared a small little makefile for you.\
It's outside the DocRoot in the directory **/var/www**

```bash
cd /var/www
make
```

```bash
watch-storefront               #Starts watcher for storefront at http://localhost:9998
        
watch-admin                    #Starts watcher for admin at http://localhost:5173
```

### **Admin Watcher**

* **Ports**\
  You need to expose the correct ports of your Docker container.\
  Please expose the port "**5173**"".<br>
* **Start Make Command**\
  Connect into your container and navigate to the folder "/var/www".\
  This prepared makefile contains a command to start the admin watcher.<br>
* **Open Browser**\
  If the make command has finished, your watcher should be ready.\
  Just open the URL in your browser and you should see the administration.\
  Please keep in mind, that different Shopware versions require different URLs to be used.\
  [http://localhost:5173](http://localhost:5173/)

\
As soon as you modify any files in the administration and upload them to the container, the watcher will recognize the changes, and compile the app again. In addition to this, it will also automatically refresh your browser (Hot Module Replacement).\
\
To stop the watcher, simply cancel the command / process.



### **Storefront Watcher**

The watcher for the storefront requires a few things to be set.\
Follow this simple guide to get started.

1. **Ports**\
   You need to expose the correct ports of your Docker container.\
   Please expose the ports "**9999**" and "**9998**".\
   The easiest way is to just expose them directly with "9999:9999" and "9998:9998" in your Docker setup.<br>
2. **Start Make Command**\
   Connect into your container and navigate to the folder "/var/www".\
   This prepared makefile contains a command to start the storefront watcher.<br>
3. **Open Browser**\
   If the make command has finished, your watcher should be ready.\
   Just open the URL in your browser and you should see that the CSS styles are loaded in a lazy way. That means it is working.\
   Please keep in mind, that different Shopware versions require different URLs to be used.\
   [http://localhost:9998](http://localhost:9998)

<br>
