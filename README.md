# Joomla-docker-container
This project is able to setup a docker container and install a fully functional joomla site in just under few minutes with its own database

Create an empty folder wherever you wish to work and then add to it a subfolder named ops. Navigate to it and create the following docker-compose.yml file.

version: '3'

services:
  joomla:
    image: joomla
    restart: always
    links:
      - joomladb:mysql
    ports:
      - 8080:80
    volumes:
      - "../:/var/www/html"
    environment:
      JOOMLA_DB_HOST: joomladb
      JOOMLA_DB_PASSWORD: example

  joomladb:
    image: mysql:5.6
    ports:
      - 3306
    restart: always
    volumes:
      - "./data:/var/lib/mysql"
    environment:
      MYSQL_ROOT_PASSWORD: example


This piece of code will be launching two services, one for joomla and another one for the database named joomladb. The links: part specifies that the service will be connecting to the joomladb:mysql service.



The image is telling docker to pull the joomla image from the docker hub. This image is basically a small linux server with apache and a fresh install of joomla.



The "ports" section defines that docker shall reroute the port 80 from the application to the port 8080 of the hosting machine (in this case , your laptop / PC). With ports you open the connections via that port from all machines. So watch out because in our example we are opening the port 3306 for everyone to listen to.



I like to do this in development mode so I can access the database using other sql explorers like Navicat SQL. It just makes everything so much more comfortable. Without this directive, then only the joomla server will be able to connect to the joomladb mysql service.



Containers for the linked service are reachable at a hostname identical to the alias, or the service name if no alias was specified.



"Volumes" is mounting one route from the hosting machine to the route specified into the container. In this case, we will be re-routing our parent folder (which will become our project folder) to the /var/www/html where apache stores its website files.



As you can also see, we do the same thing for the joomladb service, so we save the database files inside of our own folder (this way , we can carry all of our website in one single folder and fire it up with a simple docker command).



"Environment" tags system environment variables for the containers and it's pretty self intuitive.



now navigate via your command line interface to the ops folder and run the command:

docker-compose up



And you will see how in console the joomla database image and the joomla php/apache server are fired up. You now have two docker instances running. If you run the command docker service ls you will see them listed. We will talk about how to run this service in a swarm mode later in this tutorial.


Setting up the joomla site.
Head on to localhost:8080 -- the address may depend on your local machine. If you cant figure out what the local IP is, try downloading Kitematic for docker. It's a really simple interface that shows you all of your docker instances.

kitematic
If you click on the Settings tab and then Hostname / Ports then you'll reach the ip.

kitematic ui
Head to your browser and proceed with the joomla installation.

Remember the credentials must match with the environment variables set up in the yml file. So in this case, username is root (since it has not been specified), hostname is joomladb, password is example. Hit next, next and you will have a fresh working joomla app.

joomla install
Remember that "volume" part we talked about earlier? If you head to your project folder you'll find all of your joomla installation files there, and the mysql data tables stored in ops/data.

joomla dir
Restoring a Joomla Site
Now, the options here are simple enough. You can either transfer your entire joomla site via FTP / SFTP from your host to this folder (replacing all of the files that you need to) and dump your SQL tables and data to your local mysql server using any sql explorer. NOTE: In this case make sure your parameters in configuration.php match those of your system. 

Why docker? What makes it so powerful?
The power within docker resides on the fact that it's a "language" for building virtual scalable systems. Let's say you wanted to add a mail server and an FTP connection to your website. Easy, simply editing the original docker-compose.yml to add the corresponding images and links would suffice. Sure, you wouldn't probably be able to configure it correctly at first, but after toying with it a bit you'd easily get the hang of it. There's also hundreds of tutorials over the internet as well as many images and projects. And Docker hub allows you to even share your images. In this example we are using built in images for joomla and mysql, but you can build your own images by yourself, compile them and run them in docker terminal.

