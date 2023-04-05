# Create a new Project

This page is for everyone who develops a custom shop as a merchant or as an agency.

At the end of this page, you have a custom Shopware 6 installation, and also the full source code and database backup locally, so you can start developing.



## 1. Start Container

We first have to decide how we want to get our project source code.\
We can either use a prepared **dockware/dev** image with an existing Shopware version and download the source code, or use a **dockware/essentials** and start right away with mounting.

We usually recommend dockware/essentials or dockware/flex for custom projects.

\
Let's use dockware/essentials, because we can immediately install everything with our single container, thanks to the built-in MySQL service.

We create a docker-compose, which need anyway sooner or later.

```yaml
version: '3'
services:
  shop:
    image: dockware/essentials:latest
    container_name: shop
    ports:
      - "80:80"
      - "3306:3306"
```

After the container has been started, we connect into it with our terminal.

```bash
docker exec -it shop bash
```

## 2. Install Shopware

The installation of Shopware is very easy.\
We use the prepared makefile command to download any Shopware version from the Shopware website and prepare it, so it can be easily installed in our browser.

```bash
cd /var/www
make download url=https://www.shopware.com/en/...
```

{% hint style="info" %}
You can use any Shopware version as URL from this page: [https://www.shopware.com/en/changelog/](https://www.shopware.com/en/changelog/)

Just right click on any install/download button and copy the URL.
{% endhint %}



The makefile will now download the ZIP file and extract into the DocRoot of the dockware container. Now simply open your browser and **install your Shopware 6** version.



## 3. Export Database

After we have initially installed and prepared our shop, let's just export the database, so that we can use it later on.

\
Connect into your container and run the following command with your database name.

```bash
mysqldump -u root -proot (dbName) > /var/www/html/database.sql
```

The full database is now exported as a backup file in the Docroot.



## 4. Download Source Code

Our final step is to download everything to our host machine.\
We need the source code as well as the database backup.

What you do with those files is totally up to you.\
One would usually add the source code to a GIT repository and the database to an internal sharing system within your company.

Let's just run these 2 commands on your host machine.

```
mkdir ./src
docker cp (containerName):/var/www/html/. ./src
```

{% hint style="success" %}
Congratulations, you have a fully local source code and database backup for your custom Shopware shop.
{% endhint %}

## 5. Restart Project

Now to the fun part when restarting your project.\
Just imagine if you start your containers again later on, you might have a source code available, but no database. This leads to problems.

So depending on how you work, there are different approaches. \
You can have your custom MySQL container that is persisted....or even persisted in dockware, all good. But what if you need to really restore our MySQL dump from above?\
Just use this either manually, or in your project "run" script automatically.

It will create a Shopware database and import your dump.

```
docker exec -i shopware bash -c "mysql -uroot -proot -e \"DROP DATABASE IF EXISTS shopware;\""
docker exec -i shopware bash -c "mysql -uroot -proot -e \"create database shopware CHARACTER SET utf8 COLLATE utf8_general_ci;\""
docker exec -i shopware mysql -uroot -proot shopware < ./database.sql
```

That's everything, you should now have your shop available again.
