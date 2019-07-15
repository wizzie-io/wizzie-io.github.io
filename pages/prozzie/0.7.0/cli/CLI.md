---
title: Prozzie Command Line Interface
version: 0.7.0
permalink: /prozzie_0.7.0_cli.html
toc: true
---

## Description

Prozzie is the main entry point of Wizzie Data Plane (WDP) platform.

Prozzie CLI allows the user (or admin) to operate prozzie with no need to know
internals or advance docker or docker-compose commands.

## Synopsis

`prozzie [-h|--help] <command> [<command args>]`

## Options

`-h|--help`
: Shows prozzie CLI help

`-v|--version`
: Shows running prozzie version

## Commands

### Prozzie configuration operation

You can handle prozzie components configuration with `prozzie config` command. This command allows you to configure modules with a `wizard` assistant, `get`, `set` and `setup` prozzie components configuration and `enable` or `disable` modules.

#### Checking prozzie configuration with basic actions

You can use `get` and `set` actions to check and list all variables in a specific module.

You can get a complete key-value list of variables for a specified module:

```bash
prozzie config get <module>
```

i.e:

```bash
prozzie config get base
```

You can get a specified variable value with next command:

```bash
prozzie config get <module> <key1> <key2> ··· <keyN>···
```

i.e:

```bash
prozzie config get base INTERFACE_IP CLIENT_API_KEY
```

You can set a specific a list of variables with next action:

```bash
prozzie config set <module> <key-value-1> <key-value-2> ··· <key-value-N> ···
```

i.e:

```bash
prozzie config set base INTERFACE_IP=192.168.1.100 CLIENT_API_KEY=myAwesomeAPIKey
```

As you can see, the format of key-value pairs are `<key>`=`<value>`

Please note that the prozzie makes some verifications, and the variables can be
modified or rejected. The new `key=val` will be printed to check the actual new
variable assignment.

The command `prozzie config set` also accepts two modifiers in the case of
compose modules that accept configuration:

:`--dry-run`
Only validate configuration, do not modify anything.

:`--no-reload-prozzie`
Do NOT reload prozzie after configuration. This is useful in case you want to
modify the configuration of many modules and you don't want to wait for all the
reloads: just set this modifier in all but the last. Note that the use of this
modifier can set prozzie in an inconsistent state: Configuration files and
current connectors configuration are not synced until you do `prozzie up` or
similar.

`prozzie config` command allows you check and handle the configuration in next modules:

- [x] [**base**](/prozzie_{{page.version}}_installation.html)
- [x] [**f2K**](/prozzie_{{page.version}}_flow_protocol.html)
- [x] [**monitor**](/prozzie_{{page.version}}_snmp_protocol.html)
- [x] [**sfacctd**](/prozzie_{{page.version}}_sfacctd_protocol.html)
- [x] [**syslog**](/prozzie_{{page.version}}_syslog_protocol.html)
- [x] [**mqtt**](/prozzie_{{page.version}}_mqtt_protocol.html)

#### Prozzie config actions

Prozzie config command has next actions:

`prozzie config get`
: Allow you get information about prozzie component configuration

`prozzie config set`
: Allow you handle prozzie component configuration

`prozzie config wizard`
: Allow you configure all available modules with wizard assistant

`prozzie config describe <module>`
: Shows what variables have a specific module

`prozzie config setup <module>`
: Allow you configure a module with setup assistant

`prozzie config describe-all`
: Shows all variables of each module

`prozzie config enable <module1, module2, ···, moduleN>`
: Enable selected modules to run with `prozzie up` command

`prozzie config disable <module1, module2, ···, moduleN>`
: Disable selected modules to avoid run it with `prozzie up` command

`prozzie config list-enabled [--quiet|-q]`
: Shows only enabled modules. The option `--quiet` will not print the title,
making it more suitable for scripting.

Prozzie config command has next options:

`prozzie config [-h|--help]`
: Shows prozzie config subcommand help

### Prozzie service operation

You have the next commands for basic prozzie operation:

`prozzie compose`
: Send generic commands to prozzie docker compose

`prozzie down`
: Stop prozzie services and remove kafka queue

`prozzie start`
: Start prozzie services

`prozzie stop`
: Stop prozzie services

`prozzie up`
: (re)Create and start prozzie services

You can start, stop, create or destroy prozzie compose with installed commands
`prozzie start`, `prozzie stop`, `prozzie up` and `prozzie down`, respectively.

The difference between `up`/`down` and `start`/`stop` is that the former will
create or destroy containers and associated data, but the latter will only start
or stop them if they are already created with former commands.

To operate at low level on created compose, you can use `prozzie compose`
command, and it will forward arguments with proper compose
file and configurations.

So, `prozzie start`, `prozzie stop`, `prozzie up` and
`prozzie down` are just shortcuts for the long version
`prozzie compose [up|down|...]`, and arguments will be also forwarded.

### Prozzie components logs

You can use the command `logs` to see the different prozzie components logs:

```bash
prozzie logs
```

You can keep seeing logs with the option `-f/--follow`:

```bash
prozzie logs -f
```

And check only a specific component if you append that component's name. For
example, to check kafka logs:

```bash
prozzie logs kafka
```

If log file is too long, you can use `--tail <n>` option to show the last part
of log, where `<n>` is the number of lines to show:

```bash
prozzie logs -f --tail 1000 kafka
```

### Prozzie message queue operation

#### Topic management

You can manage topics with `prozzie kafka topics` subcommand. If you execute
it, you can check the options it offers to you. Check included examples in
this document.

##### Creating topics

```bash
prozzie kafka topics --create --topic abc --partitions 1 --replication-factor 1
```

Note that you don't need to create a topic before produce data. Kafka cluster
creates it for you at the same moment you produce the first message.

#### List topics

```bash
prozzie kafka topics --list
```

#### Produce messages

```bash
prozzie kafka produce <topic>
```

You can introduce as many messages as you want, separated by a newline.

#### Consume messages

```bash
prozzie kafka consume <topic> [<partition>]
```

You can consume from many topics using `--whitelist` or `--blacklist`:

```bash
prozzie kafka consume --whitelist '<topic1>|<topic2>|...'
prozzie kafka consume --blacklist '<topic1>|<topic2>|...'
```

Or consume from the earlier message in the kafka log:

```bash
prozzie kafka consume <topic> [<partition>] --from-beginning
```

#### Advanced operation

If you know how to use kafka distributed configuration scripts, you can
execute them directly using
`prozzie compose exec kafka /opt/kafka/bin/<you_script>`.

### Prozzie upgrade operation

You can check for upgrade your prozzie with `prozzie upgrade` command. If you run `prozzie upgrade` without options, prozzie will check and upgrade (With user confirmation) to latest stable release by default.

Prozzie upgrade with transactional operations. First of all Prozzie create a backup of your current prozzie version, If something go wrong then prozzie will revert all local changes.

Prozzie upgrade command has next options:

`--from-git[=<branch|commit>]`
: Upgrade prozzie from git to get changes. You can optionally reference a branch or commit. If you don't set any branch or commit, by default, Prozzie will get latest changes from git master branch.

{{site.data.alerts.warning}}
<span style="font-weight: bold">--from-git</span> operation is dangerous, you could break your prozzie! Use it at your own risk.
{{site.data.alerts.end}}

`--prerelease`
: Upgrade prozzie with latest pre-release.

`-y, --yes, --assumeyes`
: Automatic yes to prompts. Assume "yes" as answer to all prompts and run non-interactively.

`-f, --force`
: Disable checks and force upgrade to latest release.

{{site.data.alerts.warning}}
<span style="font-weight: bold">--force</span> operation is dangerous, you could break your prozzie! Use it at your own risk.
{{site.data.alerts.end}}

`--check`
: Checks for avaiable prozzie release.

`-h, --help`
: Shows `prozzie upgrade` command help.

## Creating custom subcommands

You can create your own prozzie CLI subcommands just placing it under
`<installation dir>/share/prozzie/cli/prozzie-<cmd>.bash`. For example, `foo`
subcommand would be `<installation dir>/share/prozzie/cli/prozzie-foo.bash`.
This new CLI command has to provide a short guide of what does it do via
`--shorthelp`, to be shown in `prozzie` help. Also, it will be provided
with prozzie installation prefix with `PREFIX` environment variable.

Beyond that, each prozzie CLI subcommand must provide treatment for it's
subcommands, help, and any action it wants to perform.
