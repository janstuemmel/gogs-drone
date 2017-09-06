# Gogs Drone

This repository contains everything you need to deploy a gogs server with the fully functional drone continuous integration platform.

## Introduction

[**Gogs**][gogs] (or the fork [**Gitea**][gitea]) is a webbased git frontend. It's written in [**GO**][go] and compiles to a single small binary you can run everywhere. While **GitHub** or **GitLab** have more features, Gogs is easier to setup and runs very fast, even on small machines like a RasperryPi. Gogs does not have an continuous integration platform like GitLab with GitLabCI, but you can use [**Drone**][drone], it's fully based on [**Docker**][docker], written in Go and very easy to setup.

## Goals

* We will install Gogs **not** as a docker servcie, it should run on bare metall as a **systemd** service, because we want `22` as our default ssh port on our host machine.

* Everything should have nice URLs, not like `http://126.12.21.43:7889`. So we're using [**Caddy**][caddy] to proxy pass our services internally. [**Letsencrypt**][Letsencrypt] certs are generated and updated automatically. You will need a wildcard domain for this, but you can skip corresponding chapters if you have none.

* Drone runs and executes builds from and with Docker.

* Everything should be maintainable, so we're using the `docker-compose` infrastructure.


## Dependencies

* **Git** must be installed. On debian-based machines do a `apt-get install git`.

* Gogs stores data in a **Sqlite** database. On debian-based machines do a `apt-get install sqlite3`.

* This setup heavily relies on the use of Docker and Compose. Please install both according to their documentation ([Docker][docker_install], [Compose][compose_install]).


## Instructions

After this set of instructions we've created:

* two **users**:
  * `caddy` in `/home/caddy` for the http server
  * `git` in `/home/git` for Gogs and Git
* two **systemd services**:
  * `/etc/systemd/system/gogs.service`
  * `/etc/systemd/system/caddy.service`
* one **configuration file**:
  * `/etc/caddy/Caddyfile`

Log into your server and download or `git clone` this repository.

### Caddy HTTP Server

To have nice URL's and automatic TLS you can use Caddy. If you have no domain or another HTTP Server you can skip this paragraph.

```sh
# install caddy
curl https://getcaddy.com | bash

# add user caddy and create /home/caddy
useradd -m caddy

# edit Caddyfile, change to your hostname
vi Caddyfile

# copy the Caddyfile to /etc/caddy
sudp cp Caddyfile /etc/caddy

# copy that init file to systemd's services folder
sudo cp ./caddy.service /etc/systemd/system

# give execution rights
sudo chmod 664 /etc/systemd/system/caddy.service

# enable and start that service
systemctl enable caddy.service
systemctl start caddy.service
```

### Gogs

```sh
# add a user and create /home/git
useradd -m git

# change to that user
su git && cd ~

# clone this repo
git clone https://github.com/janstuemmel/gogs-drone.git .

# edit hostname twice in gogs/custom/conf/app.ini
vi gogs/costum/conf/app.ini

# download gogs binary (maybe change link to a newer version)
wget https://dl.gogs.io/0.11.29/linux_386.tar.gz
tar -xzf *tar.gz
rm *tar.gz

# change to your normal user
exit
```

After this, the `gogs.service` file must be copied into systemd's `system` directory, after that start the service.

```sh
sudo cp ./home/git/caddy.service /etc/systemd/system
sudo chmod 664 /etc/systemd/system/caddy.service

# enable and start that service
systemctl enable gogs.service
systemctl start gogs.service
```

### Drone

Drone will run and execute from Docker. I normally run Docker from my default username, but it's also nice to run it inside a user named docker.

```sh
cd ~/gogs-drone

# change localhost to your hostname in docker-compose.yml
# generate a secret via `echo $RANDOM | sha1sum`
# and replace it with INSERT_A_SECRET_STRING
vi docker-compose.yml

# start image in detached mode
docker-compose up -d
```

[gogs]: https://gogs.io/
[gitea]: https://gitea.io/en-US/
[drone]: https://drone.io/
[docker]: https://www.docker.com/
[go]: https://golang.org/
[caddy]: https://caddyserver.com/
[letsencrypt]: https://letsencrypt.org/
[docker_install]: https://docs.docker.com/engine/installation/
[compose_install]: https://docs.docker.com/compose/install/
