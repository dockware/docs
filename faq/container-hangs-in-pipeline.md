# Container hangs in Pipeline

Previous images had a bug in the way how the entrypoint was implemented.

This led to the issue, that the whole **entrypoint was not executed** at all, when executing a custom command.

So a code like this actually used PHP 7.4 instead of PHP 8.0:

```bash
docker run -e PHP_VERSION=8.0 dockware/flex:latest bash -c 'php -v'
```

We were able to fix the way how the entrypoint was called.\
This means, that it's now always called, also when providing a custom argument.

While doing this, we figured out that the container was then frozen after executing the command.\
The reason is the way how dockware should work. It should be an **everlasting container** that hosts your Apache / Shopware and should not immediately exit.

Unfortunately this is not what you want to have, when using dockware as plain **command runner**.

Again, we found a way.

\
If you now provide a custom argument like above, the entrypoint is executed, then your command is executed and afterwards the container should exit.\
If you do not provide a custom command, then it should be in a blocking mode, so that you can use it as before.

If for some reason, the container is not existing after executing your custom command, use the DOCKWARE\_CI variable either as ENV. If you Docker runner cannot provide environment variables, then it's also possible to set it within your custom command.

```bash
docker run -e DOCKWARE_CI=1 dockware/flex:latest bash -c 'php -v'

docker run dockware/flex:latest bash -c 'export DOCKWARE_CI=1 && php -v'
```

{% hint style="warning" %}
Remember: It should not be required to use the DOCKWARE\_CI variable, but if you need to, there are different ways to allow all kinds of Docker runners.
{% endhint %}
