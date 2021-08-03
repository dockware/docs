# Create Feature

t's finally time to create our new feature.  
  
dockware brings you a running GitHub version of Shopware 6.  
Still, it's not possible to always deliver the latest commit. There will be multiple new ones everyday on GitHub.  
  
We recommend pulling the latest version before you start a new feature.  
Do this by pulling the "development" and the "platform" repository on your **host** machine.  
Open your terminal and navigate to the "src" folder.

{% code title="ON HOST" %}
```text
git pull
cd platform && git pull
```
{% endcode %}

Great, we are up to date. Now just start a new feature branch that will be used for the pull request later on.

{% code title="ON HOST" %}
```text
git checkout -b my-new-feature origin/master
```
{% endcode %}

\*\*\*\*

**Upload Source / Switch Branches**

To restart your work from your updated repository, please upload the source to your docker container.

{% hint style="warning" %}
Please note, that due to branch switching it can happen that symlinks get lost!

Just remove the "composer.lock" along with the "/vendor" folder and use "composer install" one more time.

An even better and cleaner idea is to completely reinstall Shopware when starting all over.
{% endhint %}

Here is a script that completely reinstalls Shopware and makes sure that you can work smoothly:

{% code title="ON HOST" %}
```bash
cd ((outside src directory)
docker exec -it shopware bash -c 'sudo rm -rf /var/www/html/*'
docker cp ./src/. shopware:/var/www/html/
docker exec -it shopware bash -c 'sudo rm -rf composer.lock'
docker exec -it shopware bash -c 'sudo rm -rf /var/www/html/vendor'
docker exec -it shopware bash -c 'sudo chown www-data:www-data /var/www -R'
docker exec -it shopware bash -c './psh.phar install'
```
{% endcode %}

Open [http://localhost](http://localhost/) and your updated Shopware 6 should be back.
