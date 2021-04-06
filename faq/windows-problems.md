# Windows Problems

**Can't connect into container on Windows**

If your terminal says that you don't have an tty or interactive terminal, you might want to prefix **winpty** which should work for you:

{% code title="ON HOST" %}
```text
winpty docker exec -it shopware bash
```
{% endcode %}

**System cannot find the path specified in "docker cp".**

Some directories and paths are long in Shopware...really long :\).  
Unfortunately Windows might tell you this too. The good thing is, there's a fix for long file names.  
  
Please try these things:

* Move your project to **C:\** which might shorten things automatically if you've had a deeper directory before.
* If you are on Windows 10, you can enable long paths. Open the Computer Configuration &gt; Administrative Templates &gt; System -&gt; Filesystem &gt; **Enable NTFS long paths**. You can also modify the corresponding registry entry **HKLM\SYSTEM\CurrentControlSet\Control\FileSystem** to **LongPathsEnabled** \(Type: REG\_DWORD\) to remove most of the MAX\_PATH limitations.



#### Problems with log path via docker cp

{% hint style="info" %}
As this is a problem in combination with  
1. docker  
2. shopware file structure  
3. Windows  
we can't fix this for you, but we can work a bit around it
{% endhint %}

  
1. you have to open your terminal "cmd" with admin rights.  
2. it's possible that also long paths does not work so there will be some folder which can't be downloaded. In this case we recommend to zip the content within the container, copy and unzip:  
2.1 `docker exec -it CONTAINERNAME zip -r dockware.zip ./`  
2.2 `docker cp CONTAINERNAME:/var/www/html/dockware.zip C:\my\path\dockware.zip`  
3. right click zip file -&gt; unzip  
  
the unzip process will show you some notifications that file XY can't be extracted as of path length, please skip this files to get all others to your local path.

