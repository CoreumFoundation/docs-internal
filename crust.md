# crust
`crust` helps you build and run all the applications needed for development and testing.

## Prerequisites
To use `crust` you need:
- `go 1.18` or newer
- `tmux`
- `docker`

Install them manually before continuing.

## Building

Build all the required binaries and docker images by running:

```
$ crust build images
```

## Executing `znet`

`znet` is the tool used to spin up development environment running the same components which are used in production.

`znet` may be executed using two methods.
First is direct where you execute command directly:

```
$ crust znet <command> [flags]
```

Second one is by entering the znet-environment:

```
$ crust znet [flags]
(<environment-name>) [znet] $ <command> 
```

The second method saves some typing.

## Flags

All the flags are optional. Execute

```
$ crust znet <command> --help
```

to see what the default values are.

### --env

Defines name of the environment, it is visible in brackets on the left.
Each environment is independent, you may create many of them and work with them in parallel.

### --mode

Defines the list of applications to run. You may see their definitions in `crust/pkg/znet/mode.go`

## Commands

In the environment some wrapper scripts for `znet` are generated automatically to make your life easier.
Each such `<command>` calls `crust znet <command>`.

Available commands are:
- `start` - starts applications
- `stop` - stops applications
- `remove` - stops applications and removes all the resources used by the environment
- `spec` - prints specification of the environment
- `tests` - run integration tests
- `console` - starts `tmux` session containing logs of all the running applications

## Example

Basic workflow may look like this:

```
# Enter the environment:
$ crust znet
(znet) [znet] $

# Start applications
(znet) [znet] $ start

# Print spec
(znet) [znet] $ spec

# Stop applications
(znet) [znet] $ stop

# Start applications again
(znet) [znet] $ start

# Stop everything and clean resources
(znet) [znet] $ remove
$
```

## Logs

After entering and starting environment:

```
$ crust znet
(znet) [znet] $ start
```

it is possible to use `logs` wrapper to tail logs from an application:

```
(znet) [znet] $ logs coredev-00
```

## Playing with the blockchain manually

For each `cored` instance started by `znet` wrapper script named after the name of the node is created, so you may call the client manually.
There are also three standard keys: `alice`, `bob` and `charlie` added to the keystore of each instance.

If you start `znet` using `--mode=dev` there is one `cored` application called `coredev-00`.
To use the client you may use `coredev-00` wrapper:

```
(znet) [znet] $ coredev-00 keys list
(znet) [znet] $ coredev-00 query bank balances cosmos1rd8wynz2987ey6pwmkuwfg9q8hf04xdyjqy2f4
(znet) [znet] $ coredev-00 tx bank send bob cosmos1rd8wynz2987ey6pwmkuwfg9q8hf04xdyjqy2f4 10core
(znet) [znet] $ coredev-00 query bank balances cosmos1rd8wynz2987ey6pwmkuwfg9q8hf04xdyjqy2f
```

## Integration tests

Tests are defined in `crust/tests/index.go`

You may run tests directly:

```
$crust znet test
```

Tests run on top `--mode=test`.

It's also possible to enter the environment first, and run tests from there:

```
$ crust znet --mode=test
(znet) [znet] $ tests

# Remember to clean everything
(crustznet) [znet] $ remove
```

After tests complete environment is still running so if something went wrong you may inspect it manually.


## Hard reset

If you want to manually remove all the data created by `znet` do this:
- use `docker ps -a`, `docker stop <container-id>` and `docker rm <container-id>` to delete related running containers
- run `rm -rf ~/.cache/crust/znet` to remove all the files created by `znet`
