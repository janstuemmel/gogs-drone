# Gogs Drone

version controll and ci on a linux machine

## Goals

* gogs runs on bare metall
* drone executes from and with docker
* maintainable

## Dependencies

* git via `apt-get install git` (on debian based machines)
* [docker][docker_install], [docker-compose][compose_install]
* sqlite3 `apt-get install sqlite3` (on debian based machines)

## Install

```sh
# use sudo if you aren't root
useradd -m git
su git
git clone <repo_url> .

# run gogs (on port 3000)
(cd gogs && ./gogs web)

# goto: <hotname>:3000 and follow instructions

# run drone (on port 8000)
# change localhost to your hostname in docker-compose.yml
docker-compose up
```

## References

### Docs

* [Gogs][gogs_gh]
* [Gogs Config][gogs_config]
* [Drone setup][drone_setup]: Drone `docker-compose.yml`
* [Drone setup gogs][drone_setup_gogs]: Drone gogs `docker-compose.yml`

### Troubleshooting

* [Gogs Drone IP][drone_gogs_ip]: Drone resolve ip
* [Drone behind NAT][drone_nat]
* [Drone agent][drone_agent_docker]: drone-agent url in `docker-compose.yml`


[gogs_gh]: https://github.com/gogits/gogs
[gogs_config]: https://gogs.io/docs/advanced/configuration_cheat_sheet
[drone_nat]: https://discuss.drone.io/t/cloning-from-gogs-repository-is-not-working/135
[drone_agent_docker]: http://discourse.drone.io/t/solved-install-drone-gogs-version-control-system-not-configured/212/2
[drone_gogs_ip]: https://discuss.drone.io/t/connecting-gogs-and-drone/81/3
[drone_setup_gogs]: http://readme.drone.io/admin/setup-gogs/
[drone_setup]: http://readme.drone.io/admin/installation-guide/

[docker_install]: https://docs.docker.com/engine/installation/
[compose_install]: https://docs.docker.com/compose/install/
