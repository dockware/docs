# Tideways Profiling



Want to take a deep look into your code performance?  
  
Then use the PHP profiler [tideways](https://tideways.com/). You only need to do 2 things:  
  


1. Create a tideways account
2. Configure your API key in the docker environment

| Variable | Default Value | Description |
| :--- | :--- | :--- |
| TIDEWAYS\_KEY |  | Required |
| TIDEWAYS\_ENV | production | Set an environment variable to separate the profiles in tideways. The default is production, as this is in the flex plan of tideways the only available environment. |
| TIDEWAYS\_SERVICE | web | Use this to differ between different components/services of your project such as example shop, api etc. |

```yaml
dockware:
      image: dockware/dev:latest
      ...
      environment:
         - TIDEWAYS_KEY=xxx
         - TIDEWAYS_ENV=production
         - TIDEWAYS_SERVICE=myshop
```

