# Environment Variables

Here is a full list of all available environment variables. Please keep in mind, that they might differ from image to image.

{% hint style="info" %}
You will find the available variables for your image on the [Docker Hub](https://hub.docker.com/u/dockware) README page of that image.
{% endhint %}

### General

| Feature  | Variable             | Default              | Description                                                                                                                                                                                                                                                                                                                           |
| -------- | -------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SYSTEM   | TZ                   | Europe/Berlin        | Provide a custom timezone for the OS                                                                                                                                                                                                                                                                                                  |
| PHP      | PHP\_VERSION         | PHP 7.4              | Switch to any of the installed PHP versions: 8.0, 7.4, 7.3, 7.2                                                                                                                                                                                                                                                                       |
| NODE     | NODE\_VERSION        | 12                   | Switch to a different Node version (12 \| 14 \| 16)                                                                                                                                                                                                                                                                                   |
| APACHE   | APACHE\_DOCROOT      | /var/www/html/public | Sets the default DocRoot of Apache                                                                                                                                                                                                                                                                                                    |
| COMPOSER | COMPOSER\_VERSION    | not-set              | Let's you switch between composer 1 and 2.                                                                                                                                                                                                                                                                                            |
| SHOPWARE | SHOP\_DOMAIN         | localhost            | If configured, dockware will set this domain for the default sales channel as http:// and https:// versions.                                                                                                                                                                                                                          |
|          | SW\_TASKS\_ENABLED   | 0                    | If turned on, the container will automatically start cronjobs for scheduled tasks and message queue processing                                                                                                                                                                                                                        |
|          | SW\_CURRENCY         | not-set              | Switch to a different default currency for the system, like GBP. This will be used in the administration.                                                                                                                                                                                                                             |
|          | SW\_API\_ACCESS\_KEY | not-set              | Provide a custom Sales Channel API Key when you want to use the StoreAPI                                                                                                                                                                                                                                                              |
| CI/CD    | DOCKWARE\_CI         |                      | If you run containers with DOCKWARE\_CI=1 the containers will automatically quit after running your command. Use this if you use dockware as command runner in your CI/CD system. Please note, your containers should automatically exit once a custom command is provided. This is just fa fallback if they do not exit as expected. |
|          | BUILD\_PLUGIN        |                      | If provided, the container will only build the provided plugin and immeditaly quit afterwards.                                                                                                                                                                                                                                        |

### Users

| Feature     | Variable    | Default | Description                                                                          |
| ----------- | ----------- | ------- | ------------------------------------------------------------------------------------ |
| SSH USERS   | SSH\_USER   | not-set | Name of the optional new SSH user that replaces the existing one from dockware       |
|             | SSH\_PWD    | not-set | Password of the optional new SSH user that replaces the existing one from dockware   |
| MYSQL USERS | MYSQL\_USER | not-set | Optional variable to create a separate MySQL user. This is the name of the user.     |
|             | MYSQL\_PWD  | not-set | Optional variable to create a separate MySQL user. This is the password of the user. |

### Debugging

| Feature  | Variable             | Default              | Description                                                                                                                                           |
| -------- | -------------------- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| XDEBUG   | XDEBUG\_ENABLED      | 0                    | Enable or disable XDebug with either 1 or 0 as value.                                                                                                 |
|          | XDEBUG\_REMOTE\_HOST | host.docker.internal | Use default value for MAC + Windows, and 172.17.0.1 for Linux                                                                                         |
|          | XDEBUG\_CONFIG       | idekey=PHPSTORM      | IDE Key identifier for XDebug                                                                                                                         |
|          | PHP\_IDE\_CONFIG     | serverName=localhost | used for the serverName export for XDebug usage on CLI                                                                                                |
| TIDEWAYS | TIDEWAYS\_KEY        | not-set              | API Key of the Tideways project                                                                                                                       |
|          | TIDEWAYS\_ENV        | production           | Optional identifier of the environment                                                                                                                |
|          | TIDEWAYS\_SERVICE    | web                  | Optional identifier of the service                                                                                                                    |
| FILEBEAT | FILEBEAT\_ENABLED    | 0                    | Activates the Filebeat daemon service (value 1). For this please provide a manual filebeat.yml for the container. You can do this with bind-mounting. |
