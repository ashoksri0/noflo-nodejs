NoFlo Node.js runtime environment [![Build Status](https://travis-ci.org/noflo/noflo-nodejs.svg?branch=master)](https://travis-ci.org/noflo/noflo-nodejs)
=================================

This tool is designed to be used together with the [Flowhub](http://flowhub.io/) development environment
for running [NoFlo](http://noflojs.org/) networks on [Node.js](http://nodejs.org/).

## Prepare a project folder

Start by setting up a local NoFlo Node.js project. For example:

```shell
$ mkdir my-project
$ cd my-project
$ npm init
$ npm install noflo --save
$ npm install noflo-nodejs --save
```

Continue by installing whatever [NoFlo component libraries](http://noflojs.org/library/) you need, for example:

```shell
$ npm install noflo-core --save
```

If you want, this is a great time to push your project to [GitHub](https://github.com/).

## Starting the runtime

Once you have installed the runtime, it is time to start it:

```shell
$ node node_modules/.bin/noflo-nodejs --secret some-password
```

This will start a WebSocket-based NoFlo Runtime server. When started, it will 

Copy paste this URL into the browser. The Flowhub IDE will open, and automatically connect to your runtime.
To make changes hit 'Edit as Project'. You should be able to see available components and build up your system.

## Starting an existing graph

If you want to run an existing graph, you can use the `--graph` option.

    noflo-nodejs --graph graphs/MyMainGraph.json

If you want the process to exit when the network stops, you can pass `--batch`.

## Debugging

noflo-nodejs supports [flowtrace](https://github.com/flowbased/flowtrace) allows to trace & store the execution of the FBP program,
so you can debug any issues that would occur. Specify `--trace` to enable tracing.

    noflo-nodejs --graph graphs/MyMainGraph.json --trace

If you are running in `--batch` mode, the file will be dumped to disk when the program terminates.
Otherwise you can send the `SIGUSR2` to trigger dumping the file to disk.

    kill -SIGUSR2 $PID_OF_PROCESS
    ... Wrote flowtrace to: /tmp/1151020-12063-ami5vq.json

You can now use various flowtrace tools to introspect the data.
For instance, you can get a human readable log using `flowtrace-show`

    flowtrace-show /tmp/1151020-12063-ami5vq.json

    -> IN repeat CONN
    -> IN repeat DATA hello world
    -> IN stdout CONN
    -> IN stdout DATA hello world
    -> IN repeat DISC
    -> IN stdout DISC

## Host address autodetection

By default `noflo-nodejs` will attempt to autodetect the public hostname/IP of your system.
If this fails, you can specify `--host myhostname` manually.


## Signalling aliveness

`noflo-nodejs` can *optionally* ping Flowhub registry periodically to signal aliveness.
Enable by specifying `--ping true` on commandline, or set the envvar `NOFLO_RUNTIME_PING=true`


## Persistent runtime configuration

Settings can also be loaded from a  `flowhub.json` file.
By default the configuration will be read from the current working directory,
but you can change this by setting the `PROJECT_HOME` environment variable.

Environment variables and command-line options will override settings specified in config file.

Since the values are often machine and/or user specific, you usually don't want to add this file to version control.

You can either create the file by hand, or by the provided `noflo-nodejs-init` tool. See the included help information:

```shell
$ node node_modules/.bin/noflo-nodejs-init --help
```

`$ node node_modules/.bin/noflo-nodejs-init` will initialize `flowhub.json` with the `id` of the runtime.
The `user` id comes from Flowhub > User > User Identifier.

You'll need some more info to make the registration useful:
```json
{
  "id": "runtime-id-...",
  "user": "user-id-...",
  "host": "autodetect",
  "port": 3569,
  "label": "my-runtime"
}
```

To generate this file, you could run:

```shell
$ node node_modules/.bin/noflo-nodejs-init --host autodetect --port 3569 --user user-id --label "my-runtime"
```

In this case the UUID for the runtime (shown as `runtime-id` in the JSON above) would be autogenerated.

