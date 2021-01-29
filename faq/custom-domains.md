# Custom Domains

We have prepared everything for a smooth launch using http://localhost - but you don't need to stick with that.  
It's of course possible to switch domains.  
You only need 2 things to make this happen:

* Database Domain: Change the "url" field of your sales channel in the database table "sales\_channel\_domain". This should be the one you want to use.  
* "/etc/hosts" file: This file on your host is a lookup dictionary that tells your machine that a certain IP should be used for a domain. Just insert this to say that your domain is your localhost IP. 

{% code title="/etc/hosts" %}
```text
127.0.0.1        dockware-local.com
```
{% endcode %}

  
Your Shopware should now be available using this domain.

