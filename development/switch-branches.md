# Switch Branches

We start by preparing our environment.  
To develop new plugins, themes and features, in the most efficient way, we recommend a few things.  
  


**Switching Branches?**

Now that you've completed your first steps in developing with dockware, you might ask yourself "What if I switch branches?" or "What if I just want to restart my container?"  
  
That's actually not a problem, still it relies on the way you and your team work.  
The most accurate way would be to clear the container and upload your local source code.  
It might also work if you just upload your plugin again after switching branches.  
  
Here are some commands that clean your container and upload everything again - doesn't take long - trust me :\).

```text
docker exec -it shopware bash -c 'sudo rm -rf /var/www/html/*'
docker cp ./src/. shopware:/var/www/html/
docker exec -it shopware bash -c 'sudo chown www-data:www-data /var/www -R'
```



