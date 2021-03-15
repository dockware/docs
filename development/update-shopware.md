# Update Shopware

If you want to update Shopware itself, then there are 2 basic options.

* If you are a plugin manufacturer, you could simply switch to a new dockware Shopware version and upload your plugin again. That should be fine. 
* If you have full projects, then you might either want to update Shopware using composer or through the built-in Shopware updater tool. Afterwards make sure you download the source again to your host to keep it consistent. 

{% hint style="warning" %}
Please note, it might be irritating, but in this case your used container still has the older Shopware version. Due to your custom update, that tag has no effect anymore as long as you use some kind of volume mounting.  
You can set your new tag if you prefer that for visual aspects.
{% endhint %}



