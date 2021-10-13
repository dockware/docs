# Tideways Profiling

Want to take a deep look into your code performance?\
\
Then use the PHP profiler [tideways](https://tideways.com). You only need to do 2 things:

1. Create a tideways account
2. Configure your API key in the docker environment

Now just edit the environment variables of your container and fill them with your Tideways credentials.\
Your container should automatically configure and launch tideways for you.

Here's a list of available [Environment Variables](../development/environment-variables.md)

```yaml
dockware:
      image: dockware/dev:latest
      ...
      environment:
         - TIDEWAYS_KEY=xxx
         - TIDEWAYS_ENV=production
         - TIDEWAYS_SERVICE=myshop
```
