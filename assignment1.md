```
 Part1

Create a utility to host a static website via Docker with below features:

    - The website should be accessed via your team domain name i.e
        - <name>.<team-name>.com
        - mohan.ntd.com
    - The website should be up and running in below intervals
        - 10-12
        - 4-6
    - Modify the utility so that in which first half it would be printing your name and in second half your buddy name. i.e below will be the output of curl command
        - 10-12 | Hello from Mohan
        - 4-6 | Hell from Pankaj
        
Part2

Create a new container.

Create One directory Structure:

Data

 Ninjas

   Mohan
   Uma
   Shikha
   Mayank

Now make sure that if this container is getting run by Mohan then Mohan should be able to Write on Mohand Directory only and for others Mohan should only be able to read it.
```

## Part 1
### tree
```
ubuntu@ip-172-31-47-65:~/assignment1/part1$ tree
.
├── Dockerfile
└── entrypoint.sh
```

### vi entrypoint.sh
```
#!/bin/bash
while true; do
  hour=$(date +%H)
  if [ $hour -ge 10 -a $hour -lt 12 ]; then
    echo "Hello from Mohan" > /usr/share/nginx/html/index.html
  elif [ $hour -ge 16 -a $hour -lt 18 ]; then
    echo "Hello from Pankaj" > /usr/share/nginx/html/index.html
  else
    echo "Service not available" > /usr/share/nginx/html/index.html
  fi
  sleep 60
done &

nginx -g "daemon off;"
```

### vi Dockerfile
```
FROM nginx:latest

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

RUN ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime \
    && echo "Asia/Kolkata" > /etc/timezone

ENV TZ=Asia/Kolkata

ENTRYPOINT ["/entrypoint.sh"]
```

### Images
<img width="911" height="811" alt="image" src="https://github.com/user-attachments/assets/530170f9-0874-48c2-bcb8-1ae4e36d0363" />
<img width="829" height="63" alt="image" src="https://github.com/user-attachments/assets/e55ea43a-1875-4edf-8a3e-12b5ec0133bd" />

<img width="607" height="132" alt="image" src="https://github.com/user-attachments/assets/1b9af76b-2b3e-4533-9893-606297d9b437" />



## Part 2
### create folder structure
```
mkdir ninja_access
cd ninja_access
```
### vi setup.sh
```
#!/bin/bash
mkdir -p data/ninjas/{Mohan,Uma,Shikha,Mayank}
sudo chown -R 1000:1000 data/ninjas/Mohan
sudo chmod 700 data/ninjas/Mohan
sudo chmod 755 data/ninjas/Uma
sudo chmod 755 data/ninjas/Shikha
sudo chmod 755 data/ninjas/Mayank
```

### run above setup.sh
```
chmod +x setup.sh
./setup.sh
```

### vi Dockerfile
```
FROM ubuntu:latest

RUN mkdir -p /data/ninjas/{Mohan,Uma,Shikha,Mayank}
COPY ./data /data

RUN useradd -m mohan
RUN chown -R mohan:mohan /data/ninjas/Mohan
RUN chmod 700 /data/ninjas/Mohan && \
    chmod 755 /data/ninjas/Uma && \
    chmod 755 /data/ninjas/Shikha && \
    chmod 755 /data/ninjas/Mayank

USER mohan
WORKDIR /data/ninjas
CMD ["/bin/bash"]
```

### Build docker image & verify
```
docker build -t ninja_access:v1 .
docker images
```

### run container
```
docker run --name c1 -it ninja_access:v1
docker ps
docker ps -a
```

### Test Permission
```
cd Mohan
touch test.txt  

cd ../Uma
touch test2.txt

whoami       # mohan

ls -l /data/ninjas
# drwx------ 2 mohan mohan 4096 Mohan
# drwxr-xr-x 2 root  root  4096 Uma
# drwxr-xr-x 2 root  root  4096 Shikha
# drwxr-xr-x 2 root  root  4096 Mayank
```

### images
<img width="1586" height="828" alt="image" src="https://github.com/user-attachments/assets/0b4e1edc-e057-4fbd-b0ef-7bb6c6aa96ed" />
<img width="930" height="451" alt="image" src="https://github.com/user-attachments/assets/0cfb5f4c-e2a3-4824-bfde-956851c140d2" />



