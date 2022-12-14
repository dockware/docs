# Import MySQL Dump

Sometimes you just want to import your own MySQL backup file.

This can be done in a very easy way, even without bind-mounting or copying the file into the container. It also does not have to do with dockware, so it would also work in a plain MySQL container.



## Import using Terminal

Run this command, and it will automatically import the **backup.sql** file from your **database** folder into your MySQL instance.

```bash
docker exec -i shop mysql -uroot -proot shopware < ./database/backup.sql
```

## Import using Adminer

Copy the backup file into your container. Make sure to place it in the folder **/usr/share/adminer**

```bash
docker cp adminer.sql.gz containerName:/usr/share/adminer/adminer.sql.gz

```

Now open Adminer in your web browser.

Open the section where you can import a databse dump.

You should now be able to seelct your dump directly in your browser and have Adminer importing it.



{% hint style="warning" %}
Please keep in mind to adjust your database credentials, if you have a custom configuration for that.
{% endhint %}

