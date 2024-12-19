# ansible_minecraftoverviewer

This [ansible role](https://docs.ansible.com/) installs [minecraft-overviewer](https://github.com/overviewer/Minecraft-Overviewer/) as [docker container](https://www.docker.com/) managed by a [systemd](https://systemd.io/) timer.

I'm using [mide's docker image](https://github.com/mide/minecraft-overviewer). Thanks for your great work!

## System requirements

* Docker
* Systemd

## Tasks

* Template docker-compose file
* Create data volume directory
* Setup systemd service and timer files
* Start/Restart systemd timer

## Role parameters

| Variable                                             | Type                       | Mandatory? | Default                               | Description                                                                                      |
|------------------------------------------------------|----------------------------|------------|---------------------------------------|--------------------------------------------------------------------------------------------------|
| minecraftoverviewer_state                            | text                       | no         | `'present'`                           | If `absent` removes the docker configuration, systemd service and timer                          |
| minecraftoverviewer_minecraft_version                | version number             | no         | `'latest'`                            | Your minecraft server version                                                                    |
| minecraftoverviewer_image_version                    | docker image tag           | no         | `'latest'`                            | Used [mide's](https://hub.docker.com/r/mide/minecraft-overviewer) docker image version           |
| minecraftoverviewer_render_volume_directory          | absolute path              | no         | `/srv/minecraftoverviewer/render`     | Location of your render data directory (will be created if not present)                          |
| minecraftoverviewer_minecraftserver_volume_directory | absolute path              | no         | `/srv/minecraftserver/minecraft-data` | Location of your minecraft server data directory (minecraft overviewer will fail if not present) |
| minecraftoverviewer_on_calender                      | systemd timer `OnCalendar` | no         | `daily`                               | Specifies how often (or when exactly) your timer will run the minecraft overviewer               |
| minecraftoverviewer_container_name                   | docker container name      | no         | `minecraft-overviewer`                | Specifies how the name of your docker container                                                  |
| minecraftoverviewer_docker_working_directory         | absolute path              | no         | `/opt/minecraft_overviewer/docker`    | Directory where your docker-compose file will be placed                                          |

## Example playbook

### Add to `requirements.yml`:

```yaml
- name: install-minecraftoverviewer
  src: https://github.com/borisskert/ansible_minecraftoverviewer.git
  scm: git
```

### Example `playbook.yml`:

```yaml
- hosts: servers
  roles:
    - role: install-minecraftoverviewer
      minecraftoverviewer_state: present
      minecraftoverviewer_minecraft_version: 1.16.5
      minecraftoverviewer_image_version: 2020-09
      minecraftoverviewer_render_volume_directory: /srv/minecraft_overviewer/render
      minecraftoverviewer_minecraftserver_volume_directory: /srv/minecraftserver/minecraft-data
      minecraftoverviewer_container_name: minecraft-overviewer
      minecraftoverviewer_docker_working_directory: /opt/minecraft_overviewer/docker
      minecraftoverviewer_on_calender: hourly
```

## Testing

Requirements:

* [Vagrant](https://www.vagrantup.com/)
* [Qemu](https://www.qemu.org/libvirt) and [libvirt](https://libvirt.org/)
* [Ansible](https://docs.ansible.com/)
* [Molecule](https://molecule.readthedocs.io/en/latest/index.html)
* [yamllint](https://yamllint.readthedocs.io/en/stable/#)
* [ansible-lint](https://docs.ansible.com/ansible-lint/)
* [Docker](https://docs.docker.com/)

### Run within docker

```shell script
molecule test && molecule test --scenario-name all-parameters && molecule test --scenario-name state-absent
```

### Run within Vagrant

```shell script
molecule test -s vagrant-default && molecule test -s vagrant-all-parameters && molecule test --scenario-name vagrant-state-absent
```

I recommend to use [pyenv](https://github.com/pyenv/pyenv) for local testing.

## License

MIT

## Author Information

* [borisskert](https://github.com/borisskert)

## Further links

* [mide/minecraft-overviewer @ Github](https://github.com/mide/minecraft-overviewer)
* [mide/minecraft-overviewer @ dockerhub](https://hub.docker.com/r/mide/minecraft-overviewer)
