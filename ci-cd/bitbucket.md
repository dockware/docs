# Bitbucket

This page should give you instructions and some ideas on how to use dockware images in Bitbucket workflows and pipelines.

Please keep in mind, these might not always be 100% accurate samples, because some things like installing dependencies and so on, might work different in your project. In fact, some of these things are only related to projects, but you should get the idea.

{% hint style="info" %}
Are we missing something? Just let us know and we'd be happy to add it here!
{% endhint %}

## Pipeline image vs. Steps with containers&#x20;

You have 2 different options to use dockware (or any other image) within a Bitbucket pipeline.

You can either run your **full pipeline inside a dockware image**, or use dockware as a **standalone container** that hosts your Shopware (or other app) just like you would do it locally.

When do you need what? That is totally up to you, but we still have a few words on that.

Let's assume there are different commands, next to each other, that you usually also do within your container while developing your system. It would indeed make sense to just use a single image for your pipeline, and run all your commands in it. That just feels...tight..right?

On the other hand, as soon as you start to interact with your system from an "external point of view"...maybe with Cypress, then it would indeed make more sense, to launch a new container within your steps. You can even mount your source code into your container by using this variable **$BITBUCKET\_CLONE\_DIR.**

Here are short snippets for both ways.

### Pipeline Image

```yaml
image: dockware/flex:latest

pipelines:
  default:
    - step:
        name: PHPUnit
        script:
          - composer install
          - php vendor/bin/phpunit --configuration phpunit.xml
```

### Containers in Pipeline

```yaml
clone:
  depth: 1
options:
  max-time: 10

pipelines:
  default:
    - step:
        name: Check Code
        services:
          - docker
        caches:
          - docker
        script:
          - docker run --rm -p 80:80 --name=shopware -e PHP_VERSION=8.1 -v $BITBUCKET_CLONE_DIR:/var/www/html -d dockware/flex:latest
          - docker exec shopware bash -c 'make dev'
          - docker exec shopware bash -c 'make phpunit -B'
          - docker exec shopware bash -c 'make stan -B'
```

{% hint style="info" %}
Please keep in mind, the samples below work with both ways. But we cannot always show both of them! Please consider this!
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



