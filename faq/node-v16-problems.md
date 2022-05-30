# Node v16 Problems

All previous images contained Node v12.\
Because time moves on and new requirements we had to **upgrade to Node v16**.

Unfortunately this might cause troubles with dependencies such as the Node SASS Loader.

{% hint style="info" %}
We will introduce NVM soon, so you can easily switch Node versions just like you do with the PHP versions!
{% endhint %}

If you need older Node versions in the meantime, you can easily use these few lines of codes to get such a version. This example uninstalls the new version and installs Node v12:

```bash
sudo apt-get update
sudo apt-get remove -y nodejs
sudo curl -fsSL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt install -y nodejs
```
