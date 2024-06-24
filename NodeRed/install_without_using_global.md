# Install Node-RED without using global flag

[refer_1]: https://discourse.nodered.org/t/to-install-node-red-globally-or-not-to/216

- [To install Node-Red globally or not to][refer_1]

install Node-RED in separate folder

```
$ mkdir nrDemo_red4
$ cd nrDemo_red4
$ npm init -y
$ npm install --save node-red
```

add the start script in `package.json`

```
{
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node node_modules/node-red/red.js -v- u ."
  }
...
}
```

### TODO

to validate the persistence running mode using pm2...
