# Shopware Currency

The default currency of a Shopware installation is EURO.  
This can only be changed when running the installation wizard of Shopware.

With dockware we provide a way to switch the default currency also for existing Shopware 6 shops.

Use the ENV variable **SW\_CURRENCY** with an existing ISO currency value.

```yaml
shopware:
      image: dockware/play:latest
      ...
      environment:
         - SW_CURRENCY=GBP
```

This will not only change the default currency for the system to the selected currency, but also recalculate the factors.  
Your new default currency gets the base factor 1.0, and all other currencies will be calculated based on the new default.

Thanks to [Shyim](https://github.com/shyim) for helping with this ;\)

