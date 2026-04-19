# Developing Web Apps

Dockware can be used to easily develop any PHP or Node.js based applications.\
No matter if it's Symfony, Laravel, Vue.js and more.

The **dockware/web** image is available as a fully working environment for all these required technologies.

## Create Environment

Let's create a folder for our local project and place a **docker-compose.yml** file inside

```yaml
app:
  image: dockware/web:latest
  container_name: app
  ports:
    - 80:80
  volumes:
    - "./src:/var/www/html"
  environment:
    - PHP_VERSION=8.4
    - NODE_VERSION=20
```

This file will create a new **container** based on the latest version of dockware/web.\
It will mount a relative **src** folder to the **Apache DocRoot** directory inside the container.

We will use PHP 8.4 and Node 20. Please keep in mind, that availability of the versions is subject to change, (see changelogs for more).

After running the following command, we are able to access http://localhost with our empty application:

```bash
docker-compose up -d
```

## Create Application

It's now time to create your application. Just connect into the container and start with the installation and setup routine.

```bash
docker exec -it app bash
```

You should be automatically connected into the **/var/www/html** folder of the container.

Whatever you install there, is automatically available on your host thanks to the bind-mounting.\
Here is an example of installing Symfony

```bash
composer create-project symfony/skeleton my_project_name
```

If you have applications such as Vue.js and others, you might need an **additional port** after starting the dev server inside the container. Just append this to the **docker-compose.yml** entry and **restart** your environment.

{% hint style="success" %}
That's it, you now have a working development environment with a simple container
{% endhint %}

## Where to go from here?

Depending to the complexity of your project, you might require additional things such as new **containers**, **installation scripts** to automatically install dependencies and more.

Just imagine, you can also create 1 container based on dockware/web for your Vue.js application and another dockware/web container for your PHP backend system, and maybe an addtional MySQL container for the database.

This is not really part of dockware, because it's really bound to your special needs.

The great thing is, it is all **just plain Docker**, so you can basically build and connect everything you need.

Here are a few typical things you usually want in your projects

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>HTTP and SSL</td><td>Use HTTPS instead of HTTP for your projects</td><td><a href="../docker-tutorials/domains-with-https.md">domains-with-https.md</a></td></tr><tr><td>Improve Bind-Mounting</td><td>Optimize performance and structure when it comes to bind-mounting your source code</td><td><a href="../docker-tutorials/correct-bind-mounting.md">correct-bind-mounting.md</a></td></tr><tr><td>Web Image Features</td><td>Explore the features of dockware/web to improve your development envrionment.</td><td><a href="../../dockware-web/features.md">features.md</a></td></tr></tbody></table>
