#aws 
![](../figures/Pasted%20image%2020230615111954.png)

# Introduction to Containers and Docker
To get you warmed up today, we're going to explore some of the fundamentals of Docker and how to use it locally within one machine. Or in this case, within your [**AWS Cloud9**](https://catalog.us-east-1.prod.workshops.aws/event/dashboard/en-US/workshop/contdock/dockerbasics/setup/aws_event/launching_cloud9) workspace.


1. Let's start by running `docker --version` to confirm that both the client and server are there and working.
    
    ```bash
    1
    docker --version
    ```
    
2. Docker containers are built using images. Let's run the command `docker pull nginx\:latest` to pull down the latest nginx trusted image from Docker Hub.
    
    ```bash
    1
    docker pull nginx\:latest
    ```
    
3. Now run `docker images` to verify that the image is now on your local machine's Docker cache. If we start it then it won't have to pull it down from Docker Hub first.
    
    ```bash
    1
    docker images
    ```
    
4. Now let's try `docker run -d -p 8080:80 --name nginx nginx\:latest` to instantiate the nginx image as a background daemon with port 8080 on the host forwarding through to port 80 within the container
    
    ```bash
    1
    docker run -d -p 8080:80 --name nginx nginx\:latest
    ```
    
5. Run `docker ps` to see that our nginx container is running.
    
    ```bash
    1
    docker ps
    ```
    
6. Try `curl http://localhost:8080` to use the nginx container and verify it is working with its default `index.html`.
    
    ```bash
    1
    curl http://localhost:8080
    ```
    
7. Running `docker logs nginx` shows us the logs produced by nginx and the container. You should see some events that correspond to our curl requests.
    
    ```bash
    1
    docker logs nginx
    ```
    
8. Use `docker exec -it nginx /bin/bash` to start an interactive shell into the container's filesystem and constraints
    
    ```bash
    1
    docker exec -it nginx /bin/bash
    ```
    
9. From within the container, run `cd /usr/share/nginx/html` and `cat index.html` to see the content the nginx is serving which is part of the container.
    
    ```bash
    1
    2
    cd /usr/share/nginx/html
    cat index.html
    ```
    
10. Type `exit` to exit our shell within the container.
    
    ```bash
    1
    exit
    ```
    
11. Now run `docker stop nginx` to stop the container.
    
    ```bash
    1
    docker stop nginx
    ```
    
12. Try `docker ps -a` command and you should see that our container is still there but stopped. At this point it could be restarted with a `docker start nginx` if we wanted.
    
    ```bash
    1
    docker ps -a
    ```
    
13. To remove the container once and for all, use the `docker rm nginx` command.
    
    ```bash
    1
    docker rm nginx
    ```
    
14. Now you can use `docker rmi nginx\:latest` to remove the nginx image from our machine's local cache
    
    ```bash
    docker rmi nginx\:latest
    ```
    

[## Building a container image](https://catalog.us-east-1.prod.workshops.aws/event/dashboard/en-US/workshop/contdock/dockerbasics#building-a-container-image)

In this step, you use a Dockerfile to build a container image onto the instance. This sample uses an image that includes the Nginx webserver to serve a simple html page.

1. Let's start by creating a working directory for our container image
    
    ```bash
    mkdir ~/environment/container-image
    ```
    
2. Type `cd container-image` to change into that folder.
    
    ```bash
    1
    cd ~/environment/container-image
    ```
    
3. Run `touch Dockerfile` to create a blank `Dockerfile`. This file will contain a set of steps required to build out container image.
    
    ```bash
    1
    touch Dockerfile
    ```
    
4. Run the below command to update the Dockerfile content
    
```bash
    cat <<EOF > Dockerfile
    FROM nginx\:latest
    COPY index.html /usr/share/nginx/html
    EOF
```
5. Run `touch index.html` to create a blank html file which will contain a simple message.
    
    ```bash
    1
    touch index.html
    ```
    
6. Use the `echo` command line tool to pipe a simple message in to our index.html file.
    
    ```bash
    1
    echo "We've added our own custom content into the container" >> index.html
    ```
    
7. Use `docker build -t nginx:1.0 .` to build the nginx container image from our Dockerfile
    
    ```bash
    1
    docker build -t nginx:1.0 .
    ```
    
8. You can now use `docker history nginx:1.0` to see all the steps and base containers that our nginx:1.0 is built on. **Note that our change amounts to one new tiny layer on top.**
    
    ```bash
    1
    docker history nginx:1.0
    ```
    
9. Type `docker run -p 8080:80 --name nginx nginx:1.0` to run our new container. Note that we didn't specify the `-d` to make it a daemon which means it holds control of our terminal and outputs the containers logs to there which can be handy in debugging.
    
    ```bash
    1
    docker run -p 8080:80 --name nginx nginx:1.0
    ```
    
10. Open another Terminal tab (**Window -> New Terminal**)
    
11. Run `curl http://localhost:8080` in the other tab a few times and see our new content.
    
    ```bash
    1
    curl http://localhost:8080
    ```
    
12. Go back to the first tab and see the log lines sent right out to STDOUT.
    
13. At this point we could push it to Docker Hub or a private registry like [Amazon ECR](https://aws.amazon.com/ecr/)  for others to pull and run. **You will learn more about that shortly.**
    
14. Type **Ctrl-C** to exit the log output. Note that the container has been stopped but is still there by running a `docker ps -a`.
    
    ```bash
    1
    docker ps -a
    ```
    
15. Use `sudo docker inspect nginx` to see lots of info about our stopped container.
    
    ```bash
    1
    sudo docker inspect nginx
    ```
    
16. As we did earlier, use `docker rm nginx` to delete our container.
    
    ```bash
    1
    docker rm nginx
    ```
    
17. For our last magic trick, we're going to try mounting some files from the host into the container rather than embedding them in the image.
    
    ```bash
    1
    docker run -d -p 8080:80 -v /home/ec2-user/environment/container-image/index.html:/usr/share/nginx/html/index.html\:ro --name nginx nginx\:latest
    ```
    
18. Now try a `curl http://localhost:8080`. Note that even though this is the upstream nginx image from Docker Hub **our content** is there.
    
    ```bash
    1
    curl http://localhost:8080
    ```
    
19. Edit the **index.html** file and add some more things.
    
    ```bash
    1
    echo "This is another line I've added to my container" >> index.html
    ```
    
20. Try another `curl http://localhost:8080` and note the immediate changes.
    
    ```bash
    1
    curl http://localhost:8080
    ```
    
21. Finally, run `docker stop nginx` and `docker rm nginx` to stop and remove our last container.
    
    ```bash
    1
    docker stop nginx && docker rm nginx
    ```