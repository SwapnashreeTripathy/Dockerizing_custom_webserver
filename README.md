# Dockerize HTML-page With NGINX Webserver & Push it to AWS ECR
In this project we are using Docker and to containerize a HTML web page, using Nginx as the web server and Upload your Image to AWS ECR(Amazon Elastic Container Registry).<br>

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2FSwapnashreeTripathy%2FDockerizing_custom_webserver&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
# Objective:
1. [Creation and Configuration of an EC2 Instance](#Creation and Configuration of an EC2 Instance)
3. Create a HTML webpage and Nginx File Configuration to establish Reverse Proxy.
4. Write an Dockerfile to create an Image.
5. Build Docker-Compose file to run the Container.
6. Create an Public AWS ECR and Push your image into it.<br>

## Creation and Configuration of an EC2 Instance
- Create and Lunch an EC2 Instance
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/e0ac3f8f-fea2-4726-866b-8f8cb3939733)

- Install Nginx and Docker on your AWS EC2 machine.
   ```
      sudo apt-get update -y
      sudo apt install nginx -y
      sudo systemctl status nginx
      sudo apt install docker.io -y
      docker --version
   ```
  ![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/6bdf42c6-9884-45af-bfdd-f75d3a51e505)
  
## Create a HTML webpage and Nginx File Configuration to establish Reverse Proxy
- Create a plain HTML page named `index.html`
   ![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/8732a06f-b1ca-4bd1-86c8-a686bc551061)
- Create an Nginx configuration file named `nginx.conf` that serves the `index.html` page.
- Configure Nginx to listen on port 80. 
```
#-----------------------------nginx.conf-------------------------
server {
    listen 80;
    server_name _;
    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
#------------------------------------------------------------------
```
- the path "/usr/share/nginx/html" will store your "index.html" file inside your container.

## Write an Dockerfile to create an Image
- here, take the basic Image of Nginx and top os this we will create layers for Image for our HTML page.
- coping both `nginx.conf` & `index.html` file into their respective folders for the Image.
```
#----------------------------dockerfile------------------------------
FROM nginx:latest
# Copy HTML file to serve
COPY index.html /usr/share/nginx/html/index.html
COPY nginx.conf /etc/nginx/sites-available/nginx.conf
# Expose port 80
EXPOSE 80
#--------------------------------------------------------------------
```
- Create an Image by running below command.
```
git clone https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver.git
sudo docker build -t htmlapp-st<provide a name Image> .
sudo docker images
```
- Use this Command to create Docker Container Manually and We are accessing our HTML page by 8080 port while Ngnix is running at port 80.
```
sudo docker run -it -d -p 8080:80 htmlapp-st 
```

![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/1c258e73-7ef6-4a37-a463-4626f716c3e5)
- Now Access your EC2 server by using it's Public IP with port 8080.
 
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/a75f65cb-f716-4d1a-a586-631ac0781d30)

## Build Docker-Compose file to run the Container
- Instead of running the container manually you can also write an Docker-Compose file and run it.
- Install Docker-Compose.
```
sudo snap install docker
sudo apt  install docker-compose
```

```
#---------------------------------Docker-Compose.yml------------------
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: dockerfile
    ports:
      - "8080:80"
#-------------------------------------------------------------------------
```
- Run below command to Create an Image from `Docker-Compose.yml` file if not created already.
```
Docker-compose build 
```
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/3efa589e-f3fd-42a0-ad66-e26e61a457e0)
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/cda00e3a-4914-4c71-90cd-20ed0144e4c7)

- To start the container defined in your docker-compose.yml file, run the below command.
Note: You have move inside the Folder in which Docker-compose.yml file present.
```
sudo docker-compose up
```
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/2d15658b-2b43-4f81-b28d-d29fcf77b0a5)

## Create an Public AWS ECR and Push your image into it
- You can Push your Image to AWS ECR and to do this, Install and configure AWS CLI with your AWS Access Key(create an Access Key).
- let's create an AWS Access Key, by going in AWS IAM. And Download the CSV file while creating it.
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/30db67ab-7149-4da5-9e25-5a67407a77b5)
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/250f5438-0d5f-40f4-9fa3-a72ba2e36493)
```
 sudo apt install awscli -y
 aws --version
 aws configure

 AWS Access Key ID: --> Provide your Access Key ID
 AWS secret Acess Key: --> Provide your Access Key
 Default region name: ap-south-1(For Mumabi Region)
 Default output format: json
```

![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/a40f1f0d-6c06-4566-bc24-0469863ca472)

## Create an Public AWS ECR and Push your image into it

- Once the AWS CLI has been configured, Create an Public Repository in ECR and  can Push your Image to ECR by following below instrictions.
- Click on "View PUSH Command" Option in ECR to get the commands. 
- Run these command on EC2 after configuring AWS CLI.
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/3223af21-d352-4d16-9fcb-85c1dea5de7c)
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/5129445c-232a-4458-8b8f-248dacc521a6)
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/8b8c1c49-f435-4379-85f3-019a4d5de471)
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/de529c81-2627-4e01-a90d-2ce13313def4)
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/04f186fe-398f-44fd-a84d-42935e39df75)

 Voilà!!!!!You've Suceddfully Pushed your HTML webpage Image to ECR as well.

```
https://gallery.ecr.aws/<provide your ECR URI>
https://gallery.ecr.aws/c3w1m1q2/swapn-docker
``` 
![image](https://github.com/SwapnashreeTripathy/Dockerizing_custom_webserver/assets/139486876/d0adc7af-f7c9-4959-81ec-74eeee3385fd)

