# Setup GitHub Version

Let's start again with our environment.  
You need the same things as already mentioned in the dev version of Shopware.  
If you are not familiar with the dev version, you might want to read through that first.  
  
Let's take a look at our main goals for the contribution version.  
  


1. Start Docker
2. Prepare Development + IDE
3. GitHub Fork and Remote

**1. Start Docker**

Start dockware with our docker-compose.yml template file or your own file.  
We recommend creating a new folder for your project that contains your docker files.  


{% code title="docker-compose.yml" %}
```yaml
version: "3"

services:
        
    shopware:
      image: dockware/contribute:latest
      container_name: shopware
      ports:
         - "80:80"
         - "3306:3306"
         - "22:22"
         - "8888:8888"
         - "9999:9999"
      networks:
         - web

networks:
  web:
    external: false
```
{% endcode %}



**2. Prepare Development + IDE**

Download the source code, and open it in your IDE.  


{% code title="ON HOST" %}
```text
mkdir -p ./src
docker cp shopware:/var/www/html/. ./src
```
{% endcode %}

You will now see a folder "platform" that contains Shopware 6.  
  
There are indeed 2 repositories inside each other.

* **shopware/development** The main symfony project that has Shopware as dependency in the vendor folder
* **shopware/platform** This is the actual Shopware 6 source code. It should be modified in the directory "platform" and is automatically symlinked to the vendor directory of our symfony development project.

Now add the SFTP connection again to upload changes to the dockware container.

{% hint style="success" %}
Congratulations! That's it, you're ready to improve Shopware and help the community!
{% endhint %}

{% hint style="danger" %}
Please keep in mind, that you should only develop in the folder **"platform"**!
{% endhint %}

**3. GitHub Fork and Remote**

To create pull requests, you need to have a fork of the Shopware platform on GitHub.  
Open this page, sign in on the GitHub page and fork the repository:  
  
[https://github.com/shopware/platform](https://github.com/shopware/platform)  
  
Afterwards add your new fork as remote in your local git repository.  
Open your platform directory in your terminal and execute these commands with your username:

{% code title="ON HOST" %}
```text
git remote add myfork https://github.com/USERNAME/platform.git
```
{% endcode %}

