# MySQL failed

**MySQL failed on startup.**

This is a very rare error.  
It has to do with sharing of layers \(we think\).  
So in fact, we haven't been quite able to figure it out, but we have a solution.

{% hint style="success" %}
**Solution for all Images since 2020-11-05**

We have fixed that error on 5th november 2020. So just pull the newest image and the error should not happen again.

Please keep in mind that this will remove any persistent data if you do not have mounted a volume for mysql.
{% endhint %}

You have to remove the **volumes** for your container.

{% hint style="danger" %}
**Attention**

Please keep in mind that this will remove any persistent data that you have.  
Think about this step before proceeding.
{% endhint %}

We start by removing our container. Use this script with the name of your actual container \(shopware in our case\).

{% code title="ON HOST" %}
```text
docker rm -f [containername]
```
{% endcode %}

Now we have to remove the volumes. Let's find their names.

{% code title="ON HOST" %}
```text
docker volume ls
```
{% endcode %}

Figure out what volumes belong to your image \(by using their names\) and then delete the mysql \(and maybe shop\) volumes.

{% code title="OM HOST" %}
```text
docker volume rm -f [name]
```
{% endcode %}

If you now restart your docker container, it should work again. It's also time to restore your clean MySQL dump, if you have a running project ;\)

