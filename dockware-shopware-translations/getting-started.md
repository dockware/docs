# Getting Started

This is the official Shopware 6 Crowdin Docker image, powered by dockware.io.

It helps you to start the latest available Shopware 6 version with a prepared Crowdin installion for a seamlessly integrated translation experience.

Just start your Docker container, login to your existing Crowdin account and start translating Shopware 6 directly within your browser.

## Requirements

* You need to have Docker installed on your machine
* You need to have a Crowdin account provided by Shopware

## Using the image

### Start container

Start the container with the following command. This will automatically download the latest **dockware/crowdin** image from Docker Hub if not already existing locally.

```
docker run --name crowdin -p 80:80 -p 443:443 dockware/crowdin:latest
```

Congratulations, you can now open the Storefront at [http://localhost](http://localhost/) or the Administration at [http://localhost/admin](http://localhost/admin).

Sign in to Crowdin, select the language you want to translate and start translating. You can always change languages using the Crowdin overlay you see in your browser.

### Update and restart container

Every night a new image is being built. This means, you need to manually search and download the latest image from Docker Hub. Please note, you might not always need this. Only if new Shopware versions are released.

Run this command to update the image.

```
docker pull dockware/crowdin:latest 
```

If you have the container already running, you need to stop and remove it first. Just paste these 3 commands and it should all work automatically for you.

```
docker pull dockware/crowdin:latest
docker rm -f crowdin || true
docker run --name crowdin -p 80:80 -p 443:443 dockware/crowdin:latest
```

### Update translations in running container



If you have a running container and just want to get the latest translations from Shopware Github, just use the built-in CLI command. It will fetch the latest data and rebuild Shopware for you.

```
# connect into container
docker exec -it crowdin bash

# update translations
php bin/console crowdin:translations:update
```

## Troubleshoot

### Remove Container

Every code above makes sure the name "crowdin" is used for the container. This is to easily remove containers again. If names somehow get mixed on your machine, you can still easily remove containers. Just output a list of all existing containers and either remove them by **name** or **id**.

```
docker ps -a
```

Now you can remove the container by name or id.

```
# name
docker rm -f (name)
# ...or id
docker rm -f (id)
```
