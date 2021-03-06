# hms

Work in progress. Ship all kinds of services

```
npm install -g hms
```

Afterwards you should have an command line tool called `hms`.

## Getting started

First ssh to a remote server and install hms.
Then start up a terminal and a dock.

	hms terminal --dock # starts both a terminal and a dock

Optionally you can start the dock (or multiple docks as seperate processes)

	hms dock [url-to-a-terminal]

Then on your local machine add the remote

	hms remotes add my-remote username@your-server.com

hms uses ssh to contact the server so `username@your-server.com` should be similar to arguments you passed to ssh.
Then add a simple node app

	hms add my-remote my-app --start 'node .' --build 'npm install' --docks 1
	hms list my-remote my-app

The `--start` argument is your start script, `--build` is your build script and `--docks` tells hms to deploy it to 1 dock.

You are now ready to deploy your service. Goto your local app folder and do

	hms deploy my-remote my-app
	hms ps my-remote my-app

The ps output should verify that the app is running.

## Commands

All commands have the following syntax

```
hms [command] [remote] [service?] [options]
```

#### `hms remotes`

Manage and verify remotes.

* `hms remotes add [remote-name] [remote-url]` to add a new remote
* `hms remotes remove [remote-name]` to remove a remote
* `hms remotes` to list all remotes

* `--key,-k` to specify an explicit ssh key. Per default `~/.ssh/id_rsa` and the ssh agent is used.

#### `hms list [remote] [service-name?]`

List services and the configuration.

#### `hms add [remote] [service-name]`

Add a new service.

* `--start,-s` to specify a `start` script. Ie. `node .` to start a node service
* `--build,-b` to specify a `build` script. Ie. `npm install .` to install node modules
* `--docks,-d` to specify the docks to deploy to. If set to a number it will be converted to a set of docks.
* `--env,-e` to set env varibles. Format is `ENV_VAR=value;ENV_VAR2=value2

#### `hms update [remote] [service-name]`

Update an existing service. Accepts the same options as `hms add`.
Note that update will not restart the service. To do this just issue a `hms restart`

To delete an env variable just set it to an empty string, i.e. `ENV_VAR=`

#### `hms deploy [remote] [service-name]`

Upload cwd as a tarball to service-name and deploy and restart it.

* `--revision` to set a deploy revision tag. Defaults to `git describe` if you are in a git repo.

#### `hms remove [remote] [service-name]`

Stop and remove a service.

#### `hms start|stop|restart [remote] [service-name]`

Send a start/stop/restart signal the service. All restarts can be done gracefully by listening for `SIGTERM` and exiting nicely.

#### `hms ps [remote] [service-name?]`

List processes running on all docks. Omit the service name to list all processes.

* `--env` to also list the environment that the processes use.

#### `hms log [remote] [service-name?]`

Tail the log of a service. Omit the service name to tail all services.
hms does not save any logs so this is just the live tail of stdout/stderr and various events.

## Remote services

Run the following commands on your remote server that you want to deploy to

#### `hms terminal`

Starts a "build-and-distribute" hub that you can deploy to. After you deploy to a terminal it will run the build script and distribute the build to the docks.

* `--dock,-d` to also start a dock (this allows the terminal to run services as well)
* `--port,-p` to change the port the terminal binds to. Defaults to 10002
* `--db` to set the db file

#### `hms dock [remote]`

Starts a dock that can run and manage services.

* `--id,-i` to give the dock an optional id. Defaults to hostname
* `--port,-p` to change the port the dock binds to. Defaults to 10002
* `--db` to set the db file

## License

MIT
