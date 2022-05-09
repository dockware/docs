# Environment Variables

Here is a full list of all available environment variables. Please keep in mind, that they might differ from image to image.

{% hint style="info" %}
You will find the available variables for your image on the [Docker Hub](https://hub.docker.com/u/dockware) README page of that image.
{% endhint %}

### General

| Feature  | Variable         | Default              | Description                                                                                               |
| -------- | ---------------- | -------------------- | --------------------------------------------------------------------------------------------------------- |
| PHP      | PHP_VERSION      | PHP 7.4              | Switch to any of the installed PHP versions: 8.0, 7.4, 7.3, 7.2                                           |
| APACHE   | APACHE_DOCROOT   | /var/www/html/public | Sets the default DocRoot of Apache                                                                        |
| SHOPWARE | SW_CURRENCY      | not-set              | Switch to a different default currency for the system, like GBP. This will be used in the administration. |
| COMPOSER | COMPOSER_VERSION | not-set              | Let's you switch between composer 1 and 2.                                                                |
| BUILDING | BUILD_PLUGIN     |                      | If provided, the container will only build the provided plugin and immeditaly quit afterwards.            |
| CI       | DOCKWARE_CI      | not-set              | If provided, the container will run in a non-blocking fashion, allowing for usage in CI environments.            |

### Users

| Feature     | Variable   | Default | Description                                                                          |
| ----------- | ---------- | ------- | ------------------------------------------------------------------------------------ |
| SSH USERS   | SSH_USER   | not-set | Name of the optional new SSH user that replaces the existing one from dockware       |
|             | SSH_PWD    | not-set | Password of the optional new SSH user that replaces the existing one from dockware   |
| MYSQL USERS | MYSQL_USER | not-set | Optional variable to create a separate MySQL user. This is the name of the user.     |
|             | MYSQL_PWD  | not-set | Optional variable to create a separate MySQL user. This is the password of the user. |

### Debugging

| Feature  | Variable           | Default              | Description                                                                                                                                           |
| -------- | ------------------ | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| XDEBUG   | XDEBUG_ENABLED     | 0                    | Enable or disable XDebug with either 1 or 0 as value.                                                                                                 |
|          | XDEBUG_REMOTE_HOST | host.docker.internal | Use default value for MAC + Windows, and 172.17.0.1 for Linux                                                                                         |
|          | XDEBUG_CONFIG      | idekey=PHPSTORM      | IDE Key identifier for XDebug                                                                                                                         |
|          | PHP_IDE_CONFIG     | serverName=localhost | used for the serverName export for XDebug usage on CLI                                                                                                |
| TIDEWAYS | TIDEWAYS_KEY       | not-set              | API Key of the Tideways project                                                                                                                       |
|          | TIDEWAYS_ENV       | production           | Optional identifier of the environment                                                                                                                |
|          | TIDEWAYS_SERVICE   | web                  | Optional identifier of the service                                                                                                                    |
| FILEBEAT | FILEBEAT_ENABLED   | 0                    | Activates the Filebeat daemon service (value 1). For this please provide a manual filebeat.yml for the container. You can do this with bind-mounting. |
