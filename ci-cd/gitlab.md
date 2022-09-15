# Gitlab

This page should give you instructions and some ideas on how to use dockware images in Gitlab workflows and pipelines.

Please keep in mind, these might not always be 100% accurate samples, because some things like installing dependencies and so on, might work different in your project. In fact, some of these things are only related to projects, but you should get the idea.

{% hint style="info" %}
Are we missing something? Just let us know and we'd be happy to add it here!
{% endhint %}

## Building and Testing

Dockware has been built to have everything on board that you need for a smooth Shopware developer experience.

You can of course use those images in your build pipeline for whatever task you need to do. This can range from simple dependency installation, on to building of storefront and administration as well as unit testing, execution of static analyzers and more. Everything you usually need should be already installed (of course no project dependent dependencies and tools haha).

To use dockware for your actions, just add a new step and provide any of the available dockware images. Then within your scripts, do whatever you want :)

{% hint style="info" %}
Please note, to keep it simple, we've removed caching options of vendors, artifacts and more in the sample below. Let us know, if you need it :)
{% endhint %}

```yaml
phpstan:
    image: "dockware/dev:6.4.14.0"
    script:
        - cd /var/www/html/custom/plugins/MyPlugin && composer install
        - cd /var/www/html/custom/plugins/MyPlugin && php vendor/bin/phpstan analyse -c phpstan.neon .
        
phpunit:
    image: "dockware/dev:6.4.14.0"
    script:
        - cd /var/www/html/custom/plugins/MyPlugin && composer install
        - cd /var/www/html/custom/plugins/MyPlugin && php vendor/bin/phpunit --configuration phpunit.xml.dist

install:
    image: "dockware/dev:${SW_VERSION}"
    parallel:
        matrix:
            -   PHP_VERSION: [ "7.4", "8.0" ]
                SW_VERSION: [ "6.4.0.0", "6.4.1.0", "latest" ]
    script:
        - cd /var/www/html/custom/plugins/MyPlugin && composer install
        - cd /var/www/html && php bin/console plugin:refresh
        - cd /var/www/html && php bin/console plugin:install --clearCache --activate MyPlugin
        - cd /var/www/html && php bin/console plugin:list

        
```

{% hint style="success" %}
Thank you to Uwe (Kellerkinder) and Jonas (Basecom) for providing some great Gitlab examples for us!
{% endhint %}
