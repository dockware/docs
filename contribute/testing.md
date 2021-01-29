# Testing

**PHP Unit Tests**

We highly encourage you to execute the unit tests before creating a Pull Request.  
Connect into your container and run this command:

{% code title="IN DOCKER" %}
```text
./psh.phar unit
```
{% endcode %}

We know the unit tests take quite a long time.

If you want to just execute tests you have created, simply assign your custom group to your test. Then run the test suite only for the tests of your custom group.

That should be way faster

Don't forget to still run all tests before creating your pull request.

```php
/**
 * @group mysample
*/
public function testMyFeature(): void
{
   ....
}
```

{% code title="IN DOCKER" %}
```php
php ./vendor/bin/phpunit --configuration ./vendor/shopware/platform/phpunit.xml.dist --group mysample
```
{% endcode %}

**Administration Tests**

There is also a test suite for unit tests in the administration.  
You can run the Javascript tests with the following command:

{% code title="IN DOCKER" %}
```php
./psh.phar administration:unit 
```
{% endcode %}

