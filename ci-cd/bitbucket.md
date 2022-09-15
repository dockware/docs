# Bitbucket

This page should give you instructions and some ideas on how to use dockware images in Bitbucket workflows and pipelines.

Please keep in mind, these might not always be 100% accurate samples, because some things like installing dependencies and so on, might work different in your project. In fact, some of these things are only related to projects, but you should get the idea.

{% hint style="info" %}
Are we missing something? Just let us know and we'd be happy to add it here!
{% endhint %}

## Building and Testing

Dockware has been built to have everything on board that you need for a smooth Shopware developer experience.

You can of course use those images in your build pipeline for whatever task you need to do. This can range from simple dependency installation, on to building of storefront and administration as well as unit testing, execution of static analyzers and more. Everything you usually need should be already installed (of course no project dependent dependencies and tools haha).

To use dockware for your actions, just add a new step and provide any of the available dockware images. Then within your scripts, do whatever you want :)

```yaml
image: dockware/flex:latest

clone:
  depth: 1
options:
  max-time: 10

pipelines:
  default:
    - step:
        name: PHPUnit
        caches:
          - dockware-composer
          - node
        script:
          - composer install
          - php vendor/bin/phpunit --configuration phpunit.xml.dist

definitions:
  caches:
    dockware-composer: /var/www/.cache/composer
    node: /var/www/.npm    
```



