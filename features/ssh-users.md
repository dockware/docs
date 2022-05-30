# SSH Users

It's possible to create a separate SSH access that replaces the one that comes out of the box with dockware.\
\
For this, please provide the corresponding environment variables.\
This will lead to an automatically created new SSH user when the docker container is launched.\
The original default SSH user will not be available anymore!\
\
Please see our section about [Environment Variables](environment-variables.md) for more details about the available configuration settings.

```yaml
dockware:
      image: dockware/dev:latest
      ...
      environment:
         - SSH_USER=userABC
         - SSH_PWD=supersecret
```
