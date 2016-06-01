---
layout: post
title:  Docker Workshop - Part One
date:   2016/05/19
categories: docker workshop
---

What is Docker
--------------

A docker container is a minimal linux OS that only contains enough setup (i.e. environment variables, processes, files) to run our application. Because of this, they are very lightweight and can be run very quickly inside a virtual machine (VM) on your computer.

When we have our container all setup, we can deploy this same container on many different environments (staging/production server) with confidence that the container is guaranteed to work. It solves the problem where issues occur inconsistently across environments. No more “works on my machine”.

Prerequisites
-------------

In the interest of time, it'll be best for you to have docker installed prior to the workshop, and for you to pull a few Docker base images, just in case the Internet suddenly stops.

**Task: [install docker](https://docs.docker.com/mac/step_one/), and run `docker run hello-world` to ensure that docker is installed correctly.**

We'll be building several docker images. When building a docker image, we will be using base images to make life a little easier for us.

**Task: Download the next base images**

`docker pull ubuntu`

`docker pull ruby:2.2.2`

`docker pull nginx`

**Task: Clone this [github repo](https://github.com/c-j-j/docker-workshop/).**

Step Zero: Running a docker image from the Docker Hub
=========
Let's dive straight in to running a docker container from your laptop. The [Docker Hub](https://hub.docker.com/explore/ ) has lots of images that you can pull from the hub and run on your local machine.

For example, we can pull the ubuntu base image, and run `docker run ubuntu echo "Hello World"`. If you had pulled the Ubuntu image earlier, then the image will already be on your machine and you should see "Hello World" appear. If you hadn't done the step, it'll just pull it from the hub prior to running.

`docker run` starts a new container with the image you have specified, in this case we used the ubuntu image. An **image** is like a blueprint, and a **container** is the process that actually gets run based on the image.

To see what exactly is in this container, we can run bash inside the container and then start to explore what it actually consists of.

**Task: Run the following commands inside the ubuntu container; `pwd`, `ls`, `ps`, `whoami`. Run a bash session inside the container (you'll need to pass --interactive --tty as arguments)**

Step One: Building a docker image
===========
In the github repo, navigate to step_one where you will find a simple ruby file named simple.rb. Step one involves running this small program inside a docker container.

In order to put our application in a docker container, we first need to build an image for the container. We can do this by creating a Dockerfile in the root of our project. The idea is that the Dockerfile will define an image which defines the environment to run the application in.

We have a choice on how to build the image. We *could* build an image based on the Ubuntu official image, something like the following;

{% highlight ruby %}
FROM ubuntu

RUN apt-get update

RUN wget -P /root/src http://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.2.tar.gz

RUN cd /root/src; tar xvf ruby-2.2.2.tar.gz

RUN cd /root/src/ruby-2.2.2; ./configure; make install
{% endhighlight %}

This will build a base image from Ubuntu, run `apt-get update` and then install ruby 2.2.2, but that isn't necessary as the Docker Hub already contains a ruby base image that we can use instead.

**Task: Create a Dockerfile and base the image from `ruby:2.2.2`.**

We can now build an image using this Dockerfile. In the same directory as the Dockerfile, run;

`docker build .`

You should now be able to see your new image using;

`docker images`

Make a note of the image ID for the image you have just built.

**Task: Run a new container with the image ID that you just built, and run the command `ruby --version` inside the docker container to ensure the container has the correct version of ruby installed.**

In order to make working with docker easier, we can give our image a name by tagging it.

**Task: Build the image again, but tag it with the following name {your-name}/{image-name}. What is the version of the image? **

Do you now see your new image in the list of docker images you have?

Running our ruby program in the container.
======
We need to run the simple.rb inside our container. We first need to copy the ruby file into the container so it can be run.

We can copy files and directories from our local directory to the container by using the COPY command in the Dockerfile.

**Task: Copy over the simple.rb file to the container.**

We *could* run the program with `docker run chrisjordan/docker_demo ruby simple.rb`. However we can define a default command that gets run inside the Dockerfile using CMD. For example we could do `CMD ["ruby", "--version"]` to print the ruby version by default.

**Task: Set `ruby simple.rb` as the default command.**
 
Rebuild the image. Now run the new container. You should now see the output of the ruby program. Good times!

Step Two: Running a web app
=======
Go to step_two in the github repo. In here, we have a simple sinatra app that we'll be running inside a container. 

**Task: Set up the Dockerfile as follows**

1. Create a Dockerfile inside step_two

2. Base the image from the ruby base image

3. Set the working directory with `WORKDIR /var/app` 

4. Expose the port we want to access with `EXPOSE 4567`

4. Copy over the lib directory and the Gemfile

5. Add `RUN bundle install` to install the ruby dependencies.

6. Add `CMD ["ruby", "lib/app.rb"]` to run the application.

**Build the image and run the container.**

You should see the application has started. It should say that the app was started on port 4567, but you will not be able to access the app from your machine just yet.

This is because the port on your local machine needs to forward requests to the container. Stop the container, and run it again as follows;

`docker run -p "4567:4567" chrisjordan/docker_demo`

This forwards port 4567 from your machine to 4567 on the container. Now you should be able to view the app in your browser. but how you access that depends on whether you're using Docker for Mac or Docker Machine;

 Docker for Mac/Linux vs Docker Machine/VirtualBox
==========
Depending on how you installed Docker changes how you can access the container. If you have Docker for Mac, you can go to your internet browser and access the app with "localhost:4567".

If you are running Docker in a VM using VirtualBox and used `docker-machine` to start the docker VM, then you will need to find the IP address of the docker VM. You should be able to get this IP with `docker-machine ip default` (or whatever name you chose for your local docker machine). You can then view the app using "192.168.99.104:4567" or whatever the IP address of your docker VM is running on.

**Task: At the moment, every time you change something in the lib directory, the dependencies are going to be fetched during `bundle install`. Copy the Gemfile and do the bundle install before you copy over the lib directory, and then rebuild the image. You should notice that you can make changes to the lib directory, and not have to fetch the dependencies again.**

Accessing a running container
==========
We can run the docker image in daemon mode, as follows `docker run -d -p "4567:4567" chrisjordan/docker_demo`. You can see a list of all running containers by using `docker ps`.

**Task: Find the container ID of the running container, and access it with `docker exec --interactive --tty CONTAINER_ID bash`**. You can find the running ruby process with `ps -elf`, or curl the sinatra app by using `curl localhost:4567`.

Looking at logs
=========
**Task: View the logs of the running docker container by doing `docker logs CONTAINER_ID`. You can pass `--follow` to tail the log.**

Killing a running container
==========
To kill the container, execute `docker kill CONTAINER_ID`.

You should no longer see the running container by running `docker ps`.

Mounting a volume
==========
Run the sinatra container again. Try making a change to the text in lib/app.rb on your host machine to return a different message. You will not see your updated message. Which is really rather unfortunate. Please don't cry, we can fix that!

Right now the file system inside the container is isolated from your local environment. We can mount a volume, which means creating a link between the files on your machine with files on the container.

**Task: Run the docker container with a mounting volume by adding ```-v `pwd`:/var/app``` when you run the new container. This will mount the step_two directory on your host machine to /var/app on the container.**.

Now that the docker container is running, you should now see changes being picked up. Yay!

Running tests in the containers
===========
You can run tests inside your container, which is useful for CI or when another developer would like to run the unit tests without having to configure their own environment to do so.

**Task: Copy over the spec directory to the container, and run the container with the command `rspec`.**

Pushing your image to the Docker Hub
==========
Now we have our image that's built, we can push it to the [Docker Hub](https://hub.docker.com/) for the entire world to check out. For this task, you will need to have an account registered with Docker.

We need to tag the image we have built with our docker username. Find the most recently built image with `docker images`, and make a note of the image ID.

**Task: Tag the image with your username - `docker tag IMAGE_ID chrisjordan/docker_demo:1.0`, making sure you replace `chrisjordan` with your docker username. You're free to select what name you'd like to tag, I've chosen "docker_demo", and the version I've used "1.0" here.**

**NOTE: Ensure you give the image a version ID when you're pushing your image to the hub. If you don't specify a version, it'll use `latest` which can result in ambiguity between docker images.**

Log in to docker with `docker login --username=your-user-name`.

Now you're ready to push your image to the docker hub. `docker push chrisjordan/docker_demo:1.0`.

**This is going to take a while. If my calculations are correct, you'll be uploading around 200MB to the docker hub. Feel free to have a cup of tea at this time.**

Working with many containers
=======
We are now ready to move to step_three. For this step, our sinatra app will be storing the number of website hits it has. We'll be using a redis database to store this information.

According to the [Twelve Factor App Manifesto](http://12factor.net/), a database should be external to the application container. We can use Docker Compose to spin up multiple containers and to link them together so they can communicate to one another.

The step\_three directory has a Dockerfile in there already which is the same as step\_two. Go ahead and build the image in the same way you've done before. Make sure you tag the image with the format "username/image\_name:version\_number". You'll need to put this in the docker compose file.

To use docker compose, we first need to create a "docker-compose.yml" file in the root of step/_three that docker compose will be using.

**Task: Create a docker-compose.yml file in step_there with the following contents, replacing the image from "chrisjordan/docker_demo:1" with your docker image;**

{% highlight ruby %}
version: '2'
services:
  web:
    image: chrisjordan/docker_demo:1
    ports:
      - "4567:4567"
    depends_on:
      - db
{% endhighlight %}

**Task: Add a service with the name "db" and the image set to "redis".**

Spin up the containers with `docker-compose up`. This will automatically pick up your docker-compose.yml file as this is the default filename that it looks for.

Try to access the app at localhost:4567 (or `docker-machine ip default`:4567 for those not using Docker for Mac). You'll get an error because the sinatra app can't find the redis container.

When we link together containers, docker-compose allows one container to talk to another through the name of the service defined in the docker compose file. This means the web container can access the db container with the host `db:6379` (as 6479 is the default redis port).

**Task: Go ahead and correct the host name in lib/app.rb to "db".**

Kill the current docker compose process. Rebuild the image, and run docker compose again. You should now see the counter increment after every refresh.

The End
=======
I hope you've enjoyed this workshop, and are now familiar with docker containers/images. In the next workshop, we'll be deploying our docker container (somewhere, TBC).
