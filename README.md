# Artemis Explorer

<img src="public/img/explorer-logo.png" alt="Artemis Explorer logo" height="200" />

<div>

<b>Live Version: [explorer.artemisproject.io](http://explorer.artemisproject.io) </b>

<b>Gitter Channel: [giter.im/artemis-project-team/explorer](https://gitter.im/artemis-project-team/explorer) </b>
</div>
## Local installation

Download [Nodejs and npm](https://docs.npmjs.com/getting-started/installing-node "Nodejs install") if you don't have them

Install dependencies:

`npm install`

Install mongodb:

MacOS: `brew install mongodb`

Ubuntu: `sudo apt-get install -y mongodb-org`

## Populate the DB

This will fetch and parse the entire blockchain.

Setup your configuration file: `cp config.example.json config.json`

Edit `config.json` as you wish

Basic settings:
```json
{
    "nodeAddr":     "localhost",
    "wsPort":       8546,
    "startBlock":   0,
    "endBlock":     "latest",
    "quiet":        true,
    "syncAll":      true,
    "patch":        true,
    "patchBlocks":  100,
    "bulkSize":     100,
    "settings": {
        "symbol": "ARS",
        "name": "Artemis",
        "title": "Artemis Block Explorer",
        "author": "Arthur",
        "rss": "https://artemisproject.io",
        "reddit": "https://www.reddit.com/r/EthereumClassic",
        "twitter": "https://twitter.com/eth_classic",
        "linkedin": "https://www.linkedin.com/company/ethereum-classic",
        "github": "https://github.com/ethereumclassic",
        "logo": "/img/explorer-logo.png",
        "copyright": "2019 &copy; Artemis.",
        "poweredbyCustom": false,
        "poweredbyEtcImage": "/img/powered-by-etcexplorer-w.png",
        "poweredbyEtc": true,
        "useRichList": true,
        "useFiat": true,
        "miners": {
            "0xdf7d7e053933b5cc24372f878c90e62dadad5d42": "MMine"
         }
    }
}

```

| Name  | Explanation |
|-------------|-----|
| `nodeAddr` | Your node API RPC address. |
| `wsPort` | Your node API WS (Websocket) port. (RPC HTTP port is deprecated on Web3 1.0 see https://web3js.readthedocs.io/en/1.0/web3.html#value) |
| `startBlock` | This is the start block of the blockchain, should always be 0 if you want to sync the whole Artemis blockchain. |
| `endBlock` | This is usually the 'latest'/'newest' block in the blockchain, this value gets updated automatically, and will be used to patch missing blocks if the whole app goes down. |
| `quiet` | Suppress some messages. (admittedly still not quiet) |
| `syncAll` | If this is set to true at the start of the app, the sync will start syncing all blocks from lastSync, and if lastSync is 0 it will start from whatever the endBlock or latest block in the blockchain is. |
| `patch` | If set to true and below value is set, sync will iterated through the # of blocks specified. |
| `patchBlocks` | If `patch` is set to true, the amount of block specified will be check from the latest one. |
| `useRichList` | If `useRichList` is set to true, explorer will update account balance for richlist page. |
| `useFiat` | If `useFiat` is set to true, explorer will show price for account & tx page. ( Disable for testnets )|

### Mongodb Auth setting.

#### Configure MongoDB

In view of system security, most of mongoDB Admin has setup security options, So, You need to setup mongodb auth informations.
Switch to the built-in admin database:

```
$ mongo
$ > use admin
```

1. Create an administrative user  (if you have already admin or root of mongodb account, then skip it)

```
# make admin auth and role setup
$ > db.createUser( { user: "admin", pwd: "<Enter a secure password>", roles: ["root"] } )
```

And, You can make Explorer's "explorerDB" database with db user accounts "explorer" and password "some_pass_code".

```
$ > use explorerDB
$ > db.createUser( { user: "explorer", pwd: "<Enter a secure password>", roles: ["dbOwner"] } )
$ > quit()
```

Above dbuser explorer will full access explorerDB and clustor setting will be well used on monitoring the multiple sharding and replication of multiple mongodb instances.
Enable database authorization in the MongoDB configuration file /etc/mongodb.conf by appending the following lines:

```
auth=true
```

Restart MongoDB and verify the administrative user created earlier can connect:

```
$ sudo service mongodb restart
$ mongo -u admin -p your_password --authenticationDatabase=admin
```

If everything is configured correctly the Mongo Shell will connect and

```
$ > show dbs
```

will show db informations.
and You can add modified from  ./db.js:103 lines,  add auth information and mongodb connect options.

```
mongoose.connect(process.env.MONGO_URI || 'mongodb://localhost/explorerDB', {
  useMongoClient: true
  // poolSize: 5,
  // rs_name: 'myReplicaSetName',
  // user: 'explorer',
  // pass: 'yourdbpasscode'
});
```

And explore it.

### Run

The below will start both the web-gui and sync.js (which populates MongoDB with blocks/transactions).

`npm start`

You can leave sync.js running without app.js and it will sync and grab blocks based on config.json parameters

`npm run sync`

Enabling stats requires running a separate process:

`npm run stats`

Enabling richlist requires running a separate process:

`npm run rich`

You can configure intervals (how often a new data point is pulled) and range (how many blocks to go back) with the following:

`RESCAN=100:7700000 node tools/stats.js` (New data point every 100 blocks. Go back 7,700,000 blocks).

## Docker installation

Set `nodeAddr` in `config.json` to `host.docker.internal`

Run `docker-compose up`
