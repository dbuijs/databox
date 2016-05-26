# databox
Recipe to set up RStudio Server and a Postgresql Database

This is a recipe to set up an RStudio Server and Postgresql database inside Docker containers. I tested this on [Digital Ocean](www.digitalocean.com), but it should work on any machine that can run [Docker](www.docker.com).

1. Get yourself a new vm that is running Docker. On Digital Ocean, that means this one-click application: https://www.digitalocean.com/features/one-click-apps/docker/

2. Open a terminal on your new Docker VM and make sure you've got enough swap space. R needs a lot of swap space. On Digital Ocean VMs you don't get swap space by default so you need to create a swapfile (this script gives you 2 GB. Adjust as required):

```
     #! /bin/sh
     #Create 2GB swapfile and activate
     sudo install -o root -g root -m 0600 /dev/null /swapfile
     fallocate -l 2g /swapfile
     mkswap /swapfile
     swapon /swapfile
     echo "/swapfile       swap    swap    auto      0       0" | sudo tee -a /etc/fstab
     sudo sysctl -w vm.swappiness=10
     echo vm.swappiness = 10 | sudo tee -a /etc/sysctl.conf
```

3. Fire up an RStudio Server image. The rocker images work very well. I personally use the ropensci image. See https://github.com/rocker-org/rocker for more details. This command starts up a new container based on the rocker/ropensci image. Remember to change the password!

```
sudo docker run --restart always -d -p 80:8787 -v /home/rstudio:/home/rstudio -e PASSWORD=[Insert Password Here] --name ropensci_server rocker/ropensci
```

4. Fire up the postgresql image with adminer administration tool:

```
sudo docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
sudo docker run -d -p 8080:80 --name adminer --link some-postgres:db quantumobject/docker-adminer
```



