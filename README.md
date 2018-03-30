# Welcome to bPanel!

This is the official repo for the bPanel project,
a full featured, enterprise level GUI for your Bcoin Bitcoin node.

## Setup Your Environment With Docker
This is primarily a setup for development purposes
(though it could be used in production with some modification).

To spin up your webapp, server, a bcoin node on regtest, and generate
50 regtest BTC for your primary wallet, clone & navigate to this repo then:
1. Run `npm run install` to create a secrets.env file.
2. Run `docker-compose up -d` (add `--build` if you install more dependencies)
3. Navigate to http://localhost:5000 to see your webapp.
Requests to `\node` will get get forwarded to your bcoin node.

For local development, you run just the bcoin docker container (`docker-compose up -d bcoin`)
and then `npm run start:dev` (or `npm run start:poll` for Mac since webpack's watch behaves strangely
on mac sometimes) to run the app and app server from your local box.

## Updating Plugins
To install plugins, simply add the name as a string to the `plugins` array in `pluginsConfig.js`,
make sure to match the name to the package name on npm (`localPlugins` can be
used for plugins you are developing in the `plugins/local` directory). Once you save the file,
bPanel will automatically install the plugins and rebuild.

## Customizing Your Docker Environment
There are two docker services in the compose file: `app` and `bcoin`.
The app service runs the web server which serves the static files
for the front end and relays messages to a bcoin node.
You can use custom configs to connect to an existing node,
or use the bcoin docker service to spin up a bcoin node that the webapp will connect to.

### Configuration
The configs are managed through environment variables.
A config file is created and placed in the`./configs` directory mounted as a shared volume

Make sure to comment out the environment variables according to the network
you want your webapp to connect to and/or what kind of node you want to run if you're running the bcoin service.

### Connecting your real bcoin node
You can set your API key in `secrets.env` like `BCOIN_API_KEY=[YOUR-AWESOME-KEY]`.
This key can be any value you want. __DO NOT CHECK THIS FILE IN TO VERSION CONTROL.__

### Setup Scripts
Setup scripts are also supported. This will allow you to run scripts on your
node for a repeatable and predictable environment for testing or development.

Three circumstances need to be met to run a script:
1. There needs to be a js file to run in the `scripts` directory that exports a function to run
2. You need to pass the name of this file (including the extension)
as an environment variable named `BCOIN_INIT_SCRIPT` in the docker-compose
3. There should be no walletdb in the container.
This makes sure that a setup script doesn't overwrite your data
if you're mapping volumes or if you restart a container.

These checks are done in the `docker-bcoin-init.js` which sets up a node
based on the configs described above.
Setup scripts will also be passed the bcoin node object that has been created.

### Persistent DBs
To persist your bcoin node information (and skip the setup if the walletdb is persisted),
uncomment and edit the volumes in the bcoin service.
This could be useful if you're working on testnet or mainnet and don't want
to wait for a full sync to happen every time you create a new container.

## Setup without Docker
If you'd rather not use docker to run your environment,
you need to add a `./configs/bcoin.config.json` file with the
configuration setup for the bcoin node you'd like to connect to
(you can use the docker-compose.yml environment variables
that are prefaced with `BCOIN_` for a template.

Then, run `npm run start` to start the server.

## License

- Copyright (c) 2017, The Bcoin Devs (MIT License).
