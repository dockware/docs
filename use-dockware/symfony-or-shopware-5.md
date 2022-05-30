# Symfony or Shopware 5

The main focus of dockware is to focus on existing and upcoming Shopware 6 versions.

Still we heard the requests from the community and are now also providing limited support for Shopware 5 versions. Both, #play and #dev images come with additional versions of previous Shopware 5 editions.

**Run Shopware 5 with dockware**

```
docker run --rm -p 80:80 dockware/play:5.6.7
```

{% hint style="warning" %}
Please keep in mind that there is only limited support for Shopware 5. If you find any problems, just let us know and we try to find a solution.
{% endhint %}

### **FLEX for Symfony**

The latest image of dockware is "dockware/flex:latest". That one gives you a blank container that has everything you need to run all kinds of projects such as classic Symfony based applications.\
****\
**Run empty container for Symfony (or other) with dockware**

```
docker run --rm -p 80:80 dockware/flex:latest
```

