---
title: Prozzie Command Line Interface
version: latest
permalink: /prozzie_latest_cli.html
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

Prozzie config command has the next actions:

`prozzie config get`
: Allows you get information about prozzie component configuration

`prozzie config set`
: Allows you set prozzie component configuration

`prozzie config wizard`
: Allows you configure all available modules with wizard assistant

`prozzie config describe <module>`
: Shows what variables have a specific module

`prozzie config setup <module>`
: Allows you configure a module with setup assistant

`prozzie config describe-all`
: Shows all variables of each module

`prozzie config enable <module1, module2, ···, moduleN>`
: Enable selected modules to run with `prozzie up` command

`prozzie config disable <module1, module2, ···, moduleN>`
: Disable selected modules to avoid run it with `prozzie up` command

`prozzie config list-enabled [--quiet|-q]`
: Shows only enabled modules. The option `--quiet` will not print the title,
making it more suitable for scripting.

`prozzie config install`
: Allows you install new kafka-connect connector or docker-compose based file modules.

Prozzie config command has next options:

`--help|-h`
: Shows prozzie config subcommand help

`--no-reload-prozzie`
: Do NOT reload prozzie after configuration. This is useful in case you want to
modify the configuration of many modules and you don't want to wait for all the
reloads: just set this modifier in all but the last. Note that the use of this
modifier can set prozzie in an inconsistent state: Configuration files and
current connectors configuration are not synced until you do `prozzie up` or
similar.

Prozzie config `setup` and `set` has the next aditional option:

`--dry-run`
: Only validate configuration, do not modify anything.

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
prozzie config set <module> <key-1>=<value-1> <key-2>=<value-2> ··· <key-N>=<value-N>
```

i.e:

```bash
prozzie config set base INTERFACE_IP=192.168.1.100 CLIENT_API_KEY=myAwesomeAPIKey
```

As you can see, the format of key-value pairs are `<key>`=`<value>`

Please note that the prozzie makes some verifications, and the variables can be
modified or rejected. The new `key=value` will be printed to check the actual new
variable assignment.

Remember that the command `prozzie config set` also accepts `--dry-run` and `--no-reload-prozzie` modifiers.

`prozzie config` command allows you check and handle the configuration in next modules:

- [x] [**base**](/prozzie_{{page.version}}_installation.html)
- [x] [**f2K**](/prozzie_{{page.version}}_flow_protocol.html)
- [x] [**monitor**](/prozzie_{{page.version}}_snmp_protocol.html)
- [x] [**sfacctd**](/prozzie_{{page.version}}_sfacctd_protocol.html)
- [x] [**syslog**](/prozzie_{{page.version}}_syslog_protocol.html)
- [x] [**mqtt**](/prozzie_{{page.version}}_mqtt_protocol.html)

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

`prozzie up -d`
: (re)Create and start prozzie services. The `-d` flag is for using detached mode.

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

If log file is too long, you can use `--tail <n>` option to show the newest entries in log, where `<n>` is the number of entries to show. For exmaple, to show the 1000 newest entries of kafka's log we can type:

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

You can introduce as many messages as you want, separated by a newline. If you want to produce messages with key you can use the next command:

```bash
prozzie kafka produce <topic> --property "parse.key=true" --property "key.separator=,"
```

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

If you want to consume messages and show its keys you can use the next command:

```bash
prozzie kafka consume <topic> --property "print.key=true" --property "key.separator= -> "
```

#### Advanced operation

If you know how to use kafka distributed configuration scripts, you can
execute them directly using
`prozzie compose exec kafka /opt/kafka/bin/<your_script>`.

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

### Prozzie connectors and modules installation

You can add new kafka-connect connectors and modules with `prozzie config install` command. This command has the next parameters:

`--dry-run`
: Optional. Only validate the configuration, do not modify anything.

`--kafka-connector`
: Mandatory. This parameter allows us to specify the path of kafka-connect connector jar file. You can use a relative or absolute path.

`--compose-file`
: Mandatory. This parameter allows us to specify the **absolute path** of docker-compose file. You must use a absolute path due to docker-compose context.

`--config-file`
: Mandatory. This parameter allows us to specify the path of configuration bash file to handle the connector's configuration. You can use a relative or absolute path.

#### How to create a new configuration bash file

You can create a new configuration bash file for a new connector based in kafka-connect (using `--kafka-connector`) or docker-compose (using `--compose-file`). You can use a simple `json` or `yaml` scheme to generate automatically the bash file:

**JSON schema:**

To use a json schema you need to specify it with `--config-file.json` option.

`kafka-connect based connector`

```json
{
    "configs": [
        {
            "var_name": "variable.name.1",
            "default_value": "my-default-value",
            "description": "description about variable.name.1",
            "hidden": false
        }
    ]
}
```

`docker-compose file based connector`

```json
{
    "configs": [
        {
            "var_name": "ENV_VAR_1",
            "default_value": "my-default-value",
            "description": "description about ENV_VAR_1",
            "hidden": false
        }
    ]
}
```

**YAML schema**:

To use a yaml schema you need to specify it with `--config-file.yaml` option.

`kafka-connect based connector`

```yaml
configs:
    - var_name: "variable.name.1"
      default_value: "my-default-value"
      description: "description about variable.name.1"
      hidden: false
```

`docker-compose file based connector`

```yaml
configs:
    - var_name: "ENV_VAR_1"
      default_value: "my-default-value"
      description: "description about ENV_VAR_1"
      hidden: false
```

The `configs` field contains all the variables that our kafka-connect or docker-compose connector use. Each variable definition has the next fields:

**Mandatory fields**:

`var_name`
: The variable's name.

**Optional fields**:

`default_value`
: The default value for variable.

`description`
: A brief description of the variable.

`hidden`
: This field can have two possible values `true` or `false`, by default it's `false`. If you set `hidden` to `true` the variable will be classified like `module_hidden_envs` else `module_envs`.

Or you just follow the next basic template:

`kafka-connect based connector`

```bash
#!/usr/bin/env bash

. "${BASH_SOURCE%/*/*}/include/config_kcli.bash"

declare -A module_envs=(
    [var.name.1]='defaultValue1|my description 1',
    [var.name.2]='defaultValue2|my description 2'
)

declare -A module_hidden_envs=(
    [hidden.var.name.1]='defaultValue1|my description 1',
    [hidden.var.name.2]='defaultValue2|my description 2'
)
```

`docker-compose file based connector`

```bash
#!/usr/bin/env bash

. "${BASH_SOURCE%/*/*}/include/config_compose.bash"

declare -A module_envs=(
    [ENV_VAR_1]='defaultValue1|my description 1',
    [ENV_VAR_2]='defaultValue2|my description 2'
)

declare -A module_hidden_envs=(
    [HIDDEN_ENV_VAR_1]='defaultValue1|my description 1',
    [HIDDEN_ENV_VAR_2]='defaultValue2|my description 2'
)
```

{% include callout.html content="**IMPORTANT INFORMATION**: We don't take any responsibility If you create a schema or config file and then doesn't work. The user must know the variables and default values of the kafka-connect based connector or docker-compose file based connector that desire install. These variables could be specified in its repository or documentation.<br/><br/>

If you use a **kafka-connect based connector**, you must define at least the `name` property that must match with the connector's name. Also, you must add the `connector.class`. You can found more information about this topic in [Configuring Connectors](https://docs.confluent.io/3.0.0/connect/userguide.html#configuring-connectors) of Confluent docs.<br/><br/>

If you use a **docker-compose file based connector**, you only need to add the appropriate environment vars." type="warning" %}

## Creating custom subcommands

You can create your own prozzie CLI subcommands just placing it under
`<installation dir>/share/prozzie/cli/prozzie-<cmd>.bash`. For example, `foo`
subcommand would be `<installation dir>/share/prozzie/cli/prozzie-foo.bash`.
This new CLI command has to provide a short guide of what does it do via
`--shorthelp`, to be shown in `prozzie` help. Also, it will be provided
with prozzie installation prefix with `PREFIX` environment variable.

Beyond that, each prozzie CLI subcommand must provide treatment for it's
subcommands, help, and any action it wants to perform.
