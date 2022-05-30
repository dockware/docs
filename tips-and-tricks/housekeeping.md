# Housekeeping

As it is in real life - some housekeeping doesn't hurt nobody! If your hallway is loaded with lots of messy stuff, you just can't walk through it as fast as you want to.

And it's the same with Docker. Cleaning and removing unused and dangling images and volumes makes things faster again.

Removing all unused images can save Gigabytes of space on your machine.

{% code title="ON HOST" %}
```
docker image prune
```
{% endcode %}

Removing volumes that are not used by a container anymore keeps overhead small...

{% code title="ON HOST" %}
```
docker volume prune
```
{% endcode %}

For further information, please visit the [Docker website](https://docs.docker.com/config/pruning/)

{% hint style="danger" %}
**Attention**

Please keep in mind that you might lose important data when removing images or volumes.
{% endhint %}
