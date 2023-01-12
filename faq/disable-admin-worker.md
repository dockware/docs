# Disable Shopware admin worker

**It is not required to create custom Cron/Systemd tasks to replace the [shopware admin worker](https://docs.shopware.com/de/shopware-6-de/tutorials-und-faq/message-queue-und-scheduled-tasks).**<br>
The dockware image has Cron tasks included that can be [activated by flag](https://docs.dockware.io/features/environment-variables).

### 1. Create a Shopware config file
E.g. `docker/sw-config/shopware.yaml`<br>
If you already have a shopware config file, you should just add the admin worker config block.
```yaml
# Map to: /var/www/html/config/packages/shopware.yaml
shopware:
    admin_worker:
        enable_admin_worker: false
```

### 2. Mount this file in your `docker-compose.yaml`
```yaml
version: "3"

services:

   shopware:
      image: dockware/play:latest
      container_name: shopware_app
      volumes:
         - ./docker/sw-config/shopware.yaml:/var/www/html/config/packages/shopware.yaml:ro
```

### 3. Add the environment variable `SW_TASKS_ENABLED` to enable the integrated Cron tasks
```yaml
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

### 4. Restart & activate
Restart your containers to apply the new config. This can be done gracefully using `docker-compose down && docker-compose up`.
You can verify the enabled Cron tasks in the docker logs:
```sh
shopware_app_DEV | -----------------------------------------------------------
shopware_app_DEV | DOCKWARE: starting cron service....
shopware_app_DEV |  * Starting periodic command scheduler cron
shopware_app_DEV |    ...done.
shopware_app_DEV | -----------------------------------------------------------
```

### 5. Make Shopware use the updated config
It is required to disable and enable any plugin to force shopware to use the new `shopware.yaml` config.

You can check if the admin worker is disabled by using the open source [Tools Plugin from Friends of Shopware](https://store.shopware.com/en/frosh12599847132f/tools.html). 
