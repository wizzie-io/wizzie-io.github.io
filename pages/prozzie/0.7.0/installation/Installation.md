---
title: Prozzie Installation
version: 0.7.0
permalink: /prozzie_0.7.0_installation.html
toc: true
---

## Base Linux installation

### Getting installation script

#### Automagical installation

Prozzie will be downloaded & installed if you execute the next command in a
linux terminal as root user or using sudo command:

```bash
sudo bash -c "$(curl -L --header 'Accept: application/vnd.github.v3.raw' 'https://api.github.com/repos/wizzie-io/prozzie/contents/setups/linux_setup.sh?ref=0.7.0')"
```

#### Installation from github repository

Clone the repo and execute the `setups/linux_setup.sh` script that will guide
you through the entire installation:

### Installation steps

#### Base prozzie installation

You will be asked for a prozzie installation path, and you must remember it at
every change you want to make from now on.

If you have not installed docker or docker-compose yet, `linux_setup.sh` script
will install them. Next tool it needs for installation:

- `curl`

Curl is only needed to install docker. Beyond that point, all dependencies are contained in a docker image named [`prozzie-toolbox`](https://hub.docker.com/r/wizzieio/prozzie-toolbox/).

You will be asked for the variables on `Linux` section of
[VARIABLES.md](https://github.com/wizzie-io/prozzie/blob/master/VARIABLES.md).
The long description of these are:

INTERFACE_IP
: Interface IP to expose kafka (advertised hostname).

CLIENT_API_KEY
: Client API key you request to Wizz-in

ZZ_HTTP_ENDPOINT
: You WDP endpoint

#### Modules configuration

After that, you can configure different prozzie connectors introducing the name or
the number in the prompted menu:

```bash
1) aerohive
2) f2k
3) http2k
4) monitor
5) mqtt
6) sfacctd
7) syslog
Do you want to configure modules? (Enter for quit)
```

You can omit the prompt with `CONFIG_APPS` environment variable. For instance,
to configure only monitor and f2k, you can use `CONFIG_APPS='monitor f2k'`, and
you will directly be asked for these related apps. Similarly, you can omit the
whole prompt if that variable is empty, i.e., `CONFIG_APPS=''`.

If you ever want to reconfigure a specified protocol, you can run the `prozzie config wizard` command.

## Prozzie operation

You can start, stop, create or destroy prozzie compose with installed commands
`prozzie start`, `prozzie stop`, `prozzie up` and `prozzie down`, respectively.

To operate at low level on created compose, you can use
`prozzie compose` command, and it will forward arguments with proper compose
file and configurations. So, `prozzie start`, `prozzie stop`, `prozzie up` and
`prozzie down` are just shortcuts for the long version
`prozzie compose [up|down|...]`, and arguments will be also forwarded.

## Protocol installation

You can see the components installed in the next picture, so you can identify
the method to use to configure each one:

{% include image.html file="./prozzie/prozzie_components_diagram.png" url="./images/prozzie/prozzie_components_diagram.png" alt="Prozzie Components Diagram" caption="Prozzie Components Diagram" figure-style="text-align:center" caption-style="text-align:center" %}

# Prozzie uninstall

Prozzie doesn't provide any mechanism to uninstall itself. If you want to uninstall prozzie you must do manually.

When you install prozzie first time you must specify a `prefix`, by default It's `/usr/local`. In this prefix, prozzie creates following directories:

`${PREFIX}/share/prozzie`
: Contains files about prozzie cli and docker compose files.
`${PREFIX}/bin`
: Contains the symbolic link to prozzie command.
`${PREFIX}/etc/prozzie`
: Contains information about prozzie modules configuration.
`${PREFIX}/var/prozzie/backup`
: Contains prozzie backup when upgrade prozzie.

To uninstall prozzie you must follow the next steps:

1. Do `prozzie down` to stop and delete all modules docker container. Keep in mind that prozzie will stop and won't send data to WDP platform.
2. Remove `${PREFIX}/share/prozzie` and `${PREFIX}/etc/prozzie` folders.
3. Remove symbolic link named `prozzie` in `${PREFIX}/bin` directory. If `bin` folder It isn't necessary, then you can delete it.
4. Remove `${PREFIX}/var/prozzie` folder and its content.
