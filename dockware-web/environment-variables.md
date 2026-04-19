# Environment Variables

Here is a full list of all available environment variables.&#x20;

###

## APACHE\_DOCROOT

{% hint style="success" %}
Default: /var/www/html
{% endhint %}

Sets the default DocRoot of Apache

## DOCKWARE\_CI

If you run containers with DOCKWARE\_CI=1 the containers will automatically quit after running your command. Use this if you use dockware as command runner in your CI/CD system. Please note, your containers should automatically exit once a custom command is provided. This is just fa fallback if they do not exit as expected.

## NODE\_VERSION

Switch to a different Node version (12 | 14 | 16)

## PHP\_VERSION

Switch to any of the installed PHP versions: 8.0, 7.4, 7.3, 7.2

## RECOVERY\_MODE

{% hint style="success" %}
Default: 0
{% endhint %}

If enabled, nothing will be done in the entrypoint when booting dockware. This allows you to access the container on problems.

## TZ

{% hint style="success" %}
Default: Europe/Berlin
{% endhint %}

Provide a custom timezone for the container

This one is used for the operating system, but also for the corresponding PHP configuration.







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
