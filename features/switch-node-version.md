# Switch Node Version

Every dockware version (except play) allows you to switch to a different Node version by using the installed "nvm" Node Version Manager. We currently support Node v12, v14 and v16.\
\
This is done when starting your container.\
You can either use a command option when using "docker run", or set the Node version in your docker-compose.yml file.\


### **Switch with RUN command**

```bash
docker run --rm -p 80:80 -e NODE_VERSION=14 dockware/flex:latest
```

### **Switch with docker-compose**

```yaml
shopware:
      image: dockware/flex:latest
      ...
      environment:
         - NODE_VERSION=14
```

### **Switch at Runtime**

```bash
nvm use 14
nvm use 16
```



