# Code Coverage

The latest version of Xdebug changed the way how code coverage works.  
It's now depending on a specific **mode** that has to be set within the configuration of XDebug.

Dockware delivers this mode in `debug`  and not in the required `coverage` mode.

It's still easy to enable code coverage for your project.  
Just follow these steps...

### Enable XDebug

Make sure to enable XDebug either by using the [environment variable](../development/environment-variables.md) or by using the [makefile commands](scripts.md) in the folder `/var/www`

### Set Coverage Mode

You can always change the mode by **exposing** a new environment variable before your command.  
That means if you want to use code coverage for your PHPUnit tests, simply add the exposing of the correct `XDEBUG_MODE` directly before your command. 

```bash
XDEBUG_MODE=coverage php vendor/bin/phpunit
```

{% hint style="success" %}
That's it!  
Your coverage reports are now generated if configured with your testing framework!
{% endhint %}

