# <a name="setup-tips"></a>Setup tips

## Setup script

`npm run gi` runs the following in sequence:

- the shell setup script in <a href="https://github.com/kengz/aiva/tree/aiva-v3/bin/install" target="_blank"><code>bin/install</code></a>
- `npm install`
- `pip3 install -r requirements.txt`
- `bundle install`

It's save to run multiple times; they'll just update. The shell install script is necessary because ... just look at how many things get installed there.

## Custom deployment

All bot deployment commands are wrapped with `npm` inside `package.json`. For more novice users, you can customize the `scripts` in `package.json`. For example, changing "aiva" and "aivadev" to your bot name of choice.

## Monitoring

AIVA uses [`forever`](https://github.com/foreverjs/forever) to keep-alive (with 10 max restarts). `npm start` runs the process in the background, with it stdout logs written to `/logs`. `npm run debug` runs the same thing except it logs stdout to the terminal.

## Neo4j
If it's your first installation of `Neo4j`, the `bin/install` script will reset the default `username:pswd` from `neo4j:neo4j` to `neo4j:0000`, as required for direct usage. This is also consistent with the default `NEO4J_AUTH` in `.env`. If you wish to reset to your own password, use below:

```shell
neo4j start
# change your password
# note the default username:pswd is neo4j:0000
curl -X POST -d "password=YOUR_NEW_PASSWORD" -u neo4j:<OLD_PSWD> http://localhost:7474/user/neo4j/password
# access Neo4j browser GUI
open http://localhost:7474
```

## SSH Browser-forwarding

If you're hosting Neo4j on a remote machine and want to access its browser GUI on your local machine, connect to it via 

```shell
ssh -L 8080:localhost:7474 <remote_host>
```

Then you can go to `http://localhost:8080/` on your local browser.


## <a name="dependencies"></a>Dependencies

All the system dependencies are installed automatically by running `npm run gi`, which executes <a href="https://github.com/kengz/aiva/tree/aiva-v3/bin/install" target="_blank"><code>bin/install</code></a>. If you'd like to setup your VM manually, see/edit the setup script; below is the (non-exhaustive) list:

- `node.js >v5.x`
- `java jdk >7, export $JAVA_HOME path`
- `neo4j >v2.3.0`
- `neo4j shell tool`
- `python3, pip3`
- `setuptools virtualenvwrapper` (`pip3`)
- `libatlas-dev libblas-dev gfortran` (`Linux apt-get`)
- `numpy scipy tensorflow scikit-learn skflow pandas matplotlib` (`pip3`)
- `ruby, bundler`
- `forever, istanbul` (`npm -g`)

