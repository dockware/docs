# Code Styles

Please ensure that your source code uses the Shopware guidelines regarding code styles.  
  
Use the following tools and commands to scan your source code.  
Please note some features provide the option to "auto-fix" problems. Keep in mind that you need to download affected files to your host in that case. Maybe just download everything, GIT will show you all diffs anyway.  


**CS Fixer**

{% code title="IN DOCKER" %}
```text
./psh.phar fix-cs
```
{% endcode %}

**ESLint**  
Run this command do analyze and auto-fix your modifications in the administration.IN DOCKER

{% code title="IN DOCKER" %}
```text
cd platform/src/Administration/Resources/app/administration && ./node_modules/.bin/eslint --fix --ext .js,.vue src test
```
{% endcode %}

**PHPStan and Psalm**  
Last but not least, run the static analyzer to check your code quality with help of PHPStan and Psalm.

{% code title="IN DOCKER" %}
```text
./psh.phar static-analyze
```
{% endcode %}

Here is a final copy-paste script that you can use to run all these tools and also automatically download the source code again.

{% code title="ON HOST" %}
```text
docker exec -it shopware bash -c './psh.phar fix-cs'
docker exec -it shopware bash -c 'cd platform/src/Administration/Resources/app/administration && ./node_modules/.bin/eslint --fix --ext .js,.vue src test'
docker exec -it shopware bash -c './psh.phar static-analyze'
docker cp shopware:/var/www/html/platform/src/. ./src/platform/src
```
{% endcode %}

