# Error Port not available

If you start your container and you get an error such as this one, then it does only mean that you already have a service \(or another Docker container\) running on that port.

```bash
Ports are not available: listen tcp 0.0.0.0:80: bind: An attempt was made to access a socket 
in a way forbidden by its access permissions
```

"Localhost" along with the port of a container are the unique identifier in Docker.  
This means, that a container cannot be started if the port is already blocked.

To fix this please use these steps.

### 1. Verify Docker Containers

Check all your existing Docker containers and remove the one, that uses the port that you need for your new container.

```bash
# show ALL containers
docker ps -a

# remove our old container
docker rm -f (container_name)
```

### 2. Verify other services

If no Docker container uses your port, it might be a native service or another application.  
For example, a locally installed MySQL instance might already block our Port 3306, so Docker cannot use it.

In that case, make sure to either disable these services, or maybe use a different port for your Docker container if possible.



