# Releases and Versions

### Releases and Versions

This project follows two different approaches for versioning:



*   **dockware/shopware:**

    Instead of semantic versioning, this image uses the Docker tag to represent the Shopware version. This allows you to simply pull any supported Shopware 6 version as a tag, and you will always get the correct image with the correct Shopware release.\
    Pushed images are not modified anymore. If, for some reason, we might need to fix existing Shopware images, a suffix is appended to the tag.

```
dockware/shopware:6.7.2.1
dockware/shopware:6.7.2.1-v2
```



*   **dockware/shopware-essentials:**

    This base image uses semantic versioning. It will be updated whenever relevant changes are made in the operating system or packages. Any Shopware versions built afterward will automatically include these updates.

```
dockware/shopware-essentials:1.0.0
```

***

### Essentials-First Strategy

As outlined in the **CHANGELOG.md**, the primary focus is on the _shopware-essentials_ image.

Every change in its features or setup results in a new changelog entry. The bundled Shopware installation is treated as an addon on top of this essentials image.
