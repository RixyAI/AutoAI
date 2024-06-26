# AutoAI Agent User Guide

!!! note
    This guide assumes you are in the `autoai` folder, where the AutoAI Agent
    is located.

## Command Line Interface

Running `./autoai.sh` (or any of its subcommands) with `--help` lists all the possible
sub-commands and arguments you can use:

```shell
$ ./autoai.sh --help
Usage: python -m autoai [OPTIONS] COMMAND [ARGS]...

Options:
  --help  Show this message and exit.

Commands:
  run    Sets up and runs an agent, based on the task specified by the...
  serve  Starts an Agent Protocol compliant AutoAI server, which creates...
```

!!! important "For Windows users"
    On Windows, use `.\autoai.bat` instead of `./autoai.sh`.
    Everything else (subcommands, arguments) should work the same.

!!! info "Usage with Docker"
    For use with Docker, replace the script in the examples with
    `docker compose run --rm auto-ai`:

    ```shell
    docker compose run --rm auto-ai --ai-settings <filename>
    docker compose run --rm auto-ai serve
    ```

### `run` &ndash; CLI mode

The `run` sub-command starts AutoAI with the legacy CLI interface.

<details>
<summary>
<code>./autoai.sh run --help</code>
</summary>

```shell
$ ./autoai.sh run --help
Usage: python -m autoai run [OPTIONS]

  Sets up and runs an agent, based on the task specified by the user, or
  resumes an existing agent.

Options:
  -c, --continuous                Enable Continuous Mode
  -y, --skip-reprompt             Skips the re-prompting messages at the
                                  beginning of the script
  -l, --continuous-limit INTEGER  Defines the number of times to run in
                                  continuous mode
  --speak                         Enable Speak Mode
  --debug                         Enable Debug Mode
  --gpt3only                      Enable GPT3.5 Only Mode
  --gpt4only                      Enable GPT4 Only Mode
  --skip-news                     Specifies whether to suppress the output of
                                  latest news on startup.
  --install-plugin-deps           Installs external dependencies for 3rd party
                                  plugins.
  --ai-name TEXT                  AI name override
  --ai-role TEXT                  AI role override
  --constraint TEXT               Add or override AI constraints to include in
                                  the prompt; may be used multiple times to
                                  pass multiple constraints
  --resource TEXT                 Add or override AI resources to include in
                                  the prompt; may be used multiple times to
                                  pass multiple resources
  --best-practice TEXT            Add or override AI best practices to include
                                  in the prompt; may be used multiple times to
                                  pass multiple best practices
  --override-directives           If specified, --constraint, --resource and
                                  --best-practice will override the AI's
                                  directives instead of being appended to them
  --component-config-file TEXT    Path to the json configuration file.
  --help                          Show this message and exit.
```
</details>

This mode allows running a single agent, and saves the agent's state when terminated.
This means you can *resume* agents at a later time. See also [agent state].

!!! note
    For legacy reasons, the CLI will default to the `run` subcommand when none is
    specified: running `./autoai.sh run [OPTIONS]` does the same as `./autoai.sh [OPTIONS]`,
    but this may change in the future.

#### 💀 Continuous Mode ⚠️

Run the AI **without** user authorization, 100% automated.
Continuous mode is NOT recommended.
It is potentially dangerous and may cause your AI to run forever or carry out actions you would not usually authorize.
Use at your own risk.

```shell
./autoai.sh --continuous
```

To exit the program, press ++ctrl+c++

### `serve` &ndash; Agent Protocol mode with UI

With `serve`, the application exposes an Agent Protocol compliant API and serves a
frontend, by default on `http://localhost:8000`. You can configure the port it is served on with the `AP_SERVER_PORT` environment variable.

<details>
<summary>
<code>./autoai.sh serve --help</code>
</summary>

```shell
$ ./autoai.sh serve --help
Usage: python -m autoai serve [OPTIONS]

  Starts an Agent Protocol compliant AutoAI server, which creates a custom
  agent for every task.

Options:
  --debug                     Enable Debug Mode
  --gpt3only                  Enable GPT3.5 Only Mode
  --gpt4only                  Enable GPT4 Only Mode
  --install-plugin-deps       Installs external dependencies for 3rd party
                              plugins.
  --help                      Show this message and exit.
```
</details>

For more information about the API of the application, see [agentprotocol.ai](https://agentprotocol.ai).

<!-- TODO: add guide/manual for frontend -->

### Arguments

!!! attention
    Most arguments are equivalent to configuration options. See [`.env.template`][.env.template]
    for all available configuration options.

!!! note
    Replace anything in angled brackets (<>) to a value you want to specify

Here are some common arguments you can use when running AutoAI:

* Run AutoAI with a different AI Settings file

    ```shell
    ./autoai.sh --ai-settings <filename>
    ```

* Run AutoAI with a different Prompt Settings file

    ```shell
    ./autoai.sh --prompt-settings <filename>
    ```

!!! note
    There are shorthands for some of these flags, for example `-P` for `--prompt-settings`.  
    Use `./autoai.sh --help` for more information.

[.env.template]: https://github.com/RixyAI/AutoAI/tree/master/autoai/.env.template

## Agent State
[agent state]: #agent-state

The state of individual agents is stored in the `data/agents` folder. You can use this
in various ways:

* Resume your agent at a later time.
* Create "checkpoints" for your agent so you can always go back to specific points in
    its history.
* Share your agent!

## Workspace
[workspace]: #workspace

Agents can read and write files. This happens in the `workspace` folder, which
is in `data/agents/<agent_id>/`. Files outside of this folder can not be accessed by the
agent *unless* `RESTRICT_TO_WORKSPACE` is set to `False`.

!!! warning
    We do not recommend disabling `RESTRICT_TO_WORKSPACE`, unless AutoAI is running in
    a sandbox environment where it couldn't do any damage (e.g. Docker or a VM).

## Logs

Activity, Error, and Debug logs are located in `logs`.

!!! tip
    Do you notice weird behavior with your agent? Do you have an interesting use case? Do you have a bug you want to report?
    Follow the step below to enable your logs. You can include these logs when making an issue report or discussing an issue with us.

To print out debug logs:

```shell
./autoai.sh --debug
```

## Disabling Commands

The best way to disable commands is to disable or remove the [component][components] that provides them.
However, if you want to selectively disable some commands, you can use the `DISABLED_COMMANDS` config in your `.env`.
Put the names of the commands you want to disable, separated by commas.
You can find the list of commands in built-in components [here][commands].

For example, to disable python coding features, set it to the value below:

```ini
DISABLED_COMMANDS=execute_python_code,execute_python_file
```

[components]: ./components/components.md
[commands]: ./components/built-in-components.md