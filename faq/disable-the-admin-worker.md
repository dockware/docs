# Disable the admin worker

**It is not required to create custom Cron/Systemd tasks to replace the** [**Shopware admin worker**](https://docs.shopware.com/de/shopware-6-de/tutorials-und-faq/message-queue-und-scheduled-tasks).\
The dockware images have Cron tasks included that can be [activated by the flag](../features/environment-variables.md).

## 1. Create a Shopware config file

E.g. `docker/sw-config/shopware.yaml`\
If you already have a Shopware config file, you should just add the admin worker config block.

```
# Map to: /var/www/html/config/packages/shopware.yaml
shopware:
    admin_worker:
        enable_admin_worker: false
```

## 2. Mount this file in your docker-compose.yaml

```
version: "3"

services:

   shopware:
      image: dockware/play:latest
      container_name: shopware_app
      volumes:
         - ./docker/sw-config/shopware.yaml:/var/www/html/config/packages/shopware.yaml:ro
```

## 3. Add the environment variable SW\_TASKS\_ENABLED to enable the integrated Cron tasks

```
version: "3"

services:

   shopware:
      image: dockware/play:latest
      container_name: shopware_app
      volumes:
         - ./docker/sw-config/shopware.yaml:/var/www/html/config/packages/shopware.yaml:ro
      environment:
         - SW_TASKS_ENABLED=1
```

## 4. Restart & activate

Restart your containers to apply the new config.

This can be done gracefully using docker-compose down && docker-compose up.

You can verify the enabled Cron tasks in the Docker logs:

```
shopware_app_DEV | -----------------------------------------------------------
shopware_app_DEV | DOCKWARE: starting cron service....
shopware_app_DEV |  * Starting periodic command scheduler cron
shopware_app_DEV |    ...done.
shopware_app_DEV | -----------------------------------------------------------
```

## 5. Make Shopware use the updated config

It is required to disable and enable any plugin to force Shopware to use the new shopware.yaml config.

You can check if the admin worker is disabled by using the open source [Tools Plugin from Friends of Shopware.](https://store.shopware.com/en/frosh12599847132f/tools.html)







