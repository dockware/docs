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

