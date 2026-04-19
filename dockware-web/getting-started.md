# Getting Started

## What is dockware/web?

This image is designed to provide web developers with an **optimized environment** featuring multiple “latest” **PHP** and **Node** versions, along with **essential tools** like Xdebug, Tideways, and more.

Its goal is to streamline development workflows with a clean, up-to-date setup. Simply run the container and start developing PHP, Node, or other web applications—without the hassle of configuring your local environment.



## When to use this image?

This image is a versatile choice for all kinds of web development and should be your go-to option unless you require a specific setup, such as a pre-configured Shopware version.

**Possible use cases:**

• Developing applications with Symfony, Laravel, and more

• Building Vue.js applications or other Node-based projects

• Running tasks in CI/CD pipelines

• Developing Shopware while managing the full environment and database containers yourself



## Starting Containers

You can use either the latest tag or any specific tag version from the image. Please see [releases-and-versions.md](releases-and-versions.md "mention").

You can either start it directly by using `docker run`....

```bash
docker run -p 80:80 dockware/web:latest
```

...or create a **docker-compose.yml** file and start it using `docker-compose up -d`...

```yaml
website:
  image: dockware/web:latest
  ports:
    - 80:80
  volumes:
    - "./src:/var/www/html"
```

{% hint style="success" %}
And that's it.\
After starting your container, you will immediately have a working environment that can be accessed using **http://localhost** in your browser.
{% endhint %}
