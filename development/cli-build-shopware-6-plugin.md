# CLI Build Shopware 6 Plugin

It's now possible to **only build** your Shopware 6 **plugin** with a built in CLI option of our **DEV image**.

Use the environment variable **BUILD\_PLUGIN** with the **name of your plugin**.  
Now simply start your target Shopware 6 image and bind-mount your plugin temporarily into it.  
  
Every artefact of your plugin will now be built with that Shopware version and saved into your plugin folder.

```bash
# sample that builds plugin "SamplePlugin" with Shopware 6.4.4.0
docker run --rm -e BUILD_PLUGIN=SamplePlugin -v $(PWD):/var/www/html/custom/plugins/SamplePlugin dockware/dev:6.4.4.0
```



> Thanks for this feature bpesch!



