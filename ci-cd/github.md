# Github

This page should give you instructions and some ideas on how to use dockware images in Github workflows and pipelines.

Please keep in mind, these might not always be 100% accurate samples, because some things like installing dependencies and so on, might work different in your project. In fact, some of these things are only related to projects, but you should get the idea.

{% hint style="info" %}
Are we missing something? Just let us know and we'd be happy to add it here!
{% endhint %}

## Cypress Tests

This is a perfect usage for dockware and its pre-installed Shopware version including demo data.\
Let's assume we have a plugin where we already have some Cypress tests that need a Shopware instance.

We want to run all these tests of our plugin in **mutliple Shopware versions** within Github.

We start by creating a job with a **matrix strategy**, where just provide the Shopware versions that we need. In addition to this, we want to test it with different PHP versions.&#x20;

To avoid a combination of all possibilities, we just create a static list in here, but please feel free to use your own strategy configuration of Github actions!

The real pipeline is started by downloading the correct dockware version of our matrix value.\
Afterwards we start it with the `docker run` command and use the HTTPS port 443 as well as our correct PHP version. You can also use any other supported ENV variable in here.

Once started, we just give the container a bit **time to launch** MySQL and all its services. Usually you use a `waitFor` approach here, but this does also work really good.\
In addition to this, we imagine that we must not use `https://localhost` for our tests, due to some restrictions. So we even have the option to configure a different domain to be used locally within the Github Runner.

{% hint style="success" %}
Congratulations, your Docker infrastructure with dockware is done!
{% endhint %}

All we have to do now is to upload the files into our container, and intall and build our plugin as we would usually do.

Once completed, we can install Cypress and start to **run the tests against our domain** that we have just created for this pipeline.

If everything is done, we make sure to **always** download the logs and store those within our pipeline. By including the correct name of our shopware version from the matrix, we end up with a single ZIP file with logs and screenshots for all our Shopware versions.

{% hint style="info" %}
**Pro Tip:**\
****\
****If you want to see **all results** of all Shopware versions to detect patterns on problems in specific versions, then it might be a good thing to turn off "fail-fast" in the strategy.\
The full pipeline will still go on and takes longer even if problems occur, but you'll be rewarded with full results in the end!\


_strategy:_ &#x20;

&#x20;       _fail-fast: false_
{% endhint %}

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - master

jobs:

  e2e:
    name: E2E Tests | Shopware ${{ matrix.shopware }}, PHP ${{ matrix.php }}
    runs-on: ubuntu-latest
    strategy:
      matrix:
        include:
          - shopware: '6.4.7.0'
            php: '8.0'
          - shopware: '6.4.6.1'
            php: '8.0'
          - shopware: '6.4.5.1'
            php: '7.4'
    steps:

      - name: Clone Code
        uses: actions/checkout@v2

      - name: Download Docker
        run: |
          docker pull dockware/dev:${{ matrix.shopware }}
    
      - name: Start Docker
        run: |
          docker run --rm -p 443:443 --name shop --env PHP_VERSION=${{ matrix.php }} -d dockware/dev:${{ matrix.shopware }}
          sleep 30
          docker logs shop
          # now change the domain of our shop if required
          sudo echo "127.0.0.1 local.shopware.shop" | sudo tee -a /etc/hosts
          docker exec shop bash -c "mysql -u root -proot shopware -e \"UPDATE sales_channel_domain SET url='https://local.shopware.shop' WHERE url NOT LIKE 'default.%';\""
    
      - name: Upload into Docker
        run: |
          docker cp $(pwd)/. shop:/var/www/html/custom/plugins/MyPlugin
          docker exec shop bash -c 'sudo chown www-data:www-data /var/www/html/custom/plugins -R'
     
      - name: Install and Build Artifacts
        run: |
          docker exec shop bash -c 'cd /var/www/html/custom/plugins/MyPlugin && make install -B'
          docker exec shop bash -c 'cd /var/www/html/custom/plugins/MyPlugin && make build -B'
    
      - name: Install/Configure Plugin
        run: |
          docker exec shop bash -c 'php bin/console plugin:refresh'
          docker exec shop bash -c 'php bin/console plugin:install MyPlugin --activate'
          docker exec shop bash -c 'php bin/console system:config:set MyPlugin.config.MyKey ${{ secrets.MYKEY_TEST }}'
          docker exec shop bash -c 'php bin/console cache:clear'
   
      - name: Install Cypress
        run: cd tests/Cypress && make install -B

      - name: Start Cypress
        run: cd tests/Cypress && make run shopware=${{ matrix.shopware }} url=https://local.shopware.shop

      - name: Download Logs
        if: ${{ always() }}
        run: |
          mkdir -p $(pwd)/Tests/Cypress/cypress/logs/shopware
          mkdir -p $(pwd)/Tests/Cypress/cypress/logs/apache
          docker cp shop:/var/www/html/var/log/. $(pwd)/Tests/Cypress/cypress/logs/shopware
          docker cp shop:/var/log/php/. $(pwd)/Tests/Cypress/cypress/logs/apache
      
      - name: Store Cypress Results
        if: ${{ always() }}
        uses: actions/upload-artifact@v2
        with:
          name: cypress_results_sw_v${{ matrix.shopware }}
          retention-days: 1
          path: |
            Tests/Cypress/cypress/logs
            Tests/Cypress/cypress/videos
            Tests/Cypress/cypress/screenshots
            
```
