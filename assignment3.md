```
Create a DockerFile.
    Use Ubuntu latest image.
    Add your name as a Manintainer.
    Update local packages using command (apt-get update).
    Install nodejs package.
    Install npm package.
    Create a symlink using command (ln -s /usr/bin/nodejs /usr/bin/node).
    Trigger a command (npm install -g http-server)
    Add any test index.html file from local at /usr/apps/hello-docker/index.html on container.
    change your working directory to /usr/apps/hello-docker/.
    Run a command (http-server -s) on every container initialization.
    Build your dockerfile and tag it with ""yourname:docker-web""
    Run a docker container from the image that you have just created and map container 8080 port to host 8080 port.(8080:8080)
    Try accessing your webpage using ""http://<virtualmachine_ipaddress>:8080/index.html"" URL.
    Delete docker container and image from local"  
```


```
assignment3/
│
├── Dockerfile
└── index.html
```

### Dockerfile
```
# Start from the latest Ubuntu image
FROM ubuntu:latest

# Maintainer info (modern syntax)
LABEL maintainer="Vivek Ranjan"

# Update packages and install Node.js + npm
RUN apt-get update -y && \
    apt-get install -y nodejs npm && \
    [ -e /usr/bin/node ] || ln -s /usr/bin/nodejs /usr/bin/node

# Install http-server globally using npm
RUN npm install -g http-server

# Create app directory
RUN mkdir -p /usr/apps/hello-docker

# Copy local index.html into container
COPY index.html /usr/apps/hello-docker/index.html

# Set working directory
WORKDIR /usr/apps/hello-docker/

# Expose port 8080 (for http-server)
EXPOSE 8080

# Run http-server in silent mode when container starts
CMD ["http-server", "-s"]
```

### index.html
```
<!DOCTYPE html>
<html>
  <head>
    <title>Hello Docker</title>
  </head>
  <body>
    <h1>Hello from Sanket Gupta's Docker container!</h1>
  </body>
</html>
```

### docker build
```
docker build -t vivek:docker-web .
```
### verify
```
docker images
```

### docker container
```
docker run -d --name c1 -p 8080:8080 vivek:docker-web
```

### verify
```
docker ps
docker ps -a
curl localhost:8080
```

### website verification
```
http://<your_VM_or_host_IP>:8080/index.html
```

### clean up
```
docker ps              # find container ID
docker stop <container_id>
docker rm <container_id>
docker rmi vivek:docker-web
```

### Images
<img width="765" height="469" alt="image" src="https://github.com/user-attachments/assets/139818f3-d039-4371-b804-6103a5383d02" />
<img width="840" height="824" alt="image" src="https://github.com/user-attachments/assets/194b586b-d39a-4c12-bae1-d5f84d0cdeeb" />
<img width="1579" height="435" alt="image" src="https://github.com/user-attachments/assets/220af269-1197-4041-8dcc-8f9107bc1e9a" />
<img width="691" height="316" alt="image" src="https://github.com/user-attachments/assets/78dcd35c-0880-48f4-8179-4afbf5f0e36c" />
<img width="1585" height="343" alt="image" src="https://github.com/user-attachments/assets/c7bab6d6-eda8-4f0f-9cdc-4ad21a1cfbad" />







