# <a name="setup-tips"></a>Setup tips

## <a name="docker-installation"></a>Docker installation

[Docker](https://www.docker.com/) is a nice way to package and distribute complex modules, it also allows you to develop in safe isolated environment with containerization. 

The [AIVA Docker image](https://hub.docker.com/r/kengz/aiva/) (2Gb) comes ready-to-run for the repo source code. It includes all the needed components, e.g. the runtimes (nodejs, python, ruby, Java), Neo4j, the ML modules (Tensorflow, scikitlearn, scipy, numpy, spacy etc.). Depending on which OS you use, the setups are easy:

### Ubuntu

- Digital Ocean: The easiest, using their [Docker 1-click app on Ubuntu](https://www.digitalocean.com/features/one-click-apps/docker/). Docker comes installed with it.
- Ubuntu from scratch: See [this guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04) by Digital Ocean.

Then, you need `nodejs` to run basic setup for entering Docker.

```shell
# Nodejs
curl -sL https://deb.nodesource.com/setup_6.x | bash -
sudo apt-get install -y nodejs
```

Note that a Digital Ocean instance may initialize with insufficient swap memory. Here's how you can boost it:

```shell
# Ensure you have enough swap memory. Typically you'd have to run this
# Setting 1Gb swap space.
swapoff -a
sudo dd if=/dev/zero of=/swapfile bs=1024 count=1024k
sudo mkswap /swapfile
sudo swapon /swapfile
swapon -s
```

### Mac OSX

Mac needs a VM driver on top to run Docker. Here's the complete Docker installation, with `nodejs`

```shell
# Install Homebrew
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# Install Cask
brew install caskroom/cask/brew-cask
# Install docker toolbox
brew cask install dockertoolbox

# create the docker machine. Note that 'default' is the vm name we will be using
docker-machine create --driver virtualbox default
# allocate resource for the docker machine when stopped
VBoxManage modifyvm default --cpus 2 --memory 4096
docker-machine start default

# to use vm in terminal
echo 'eval "$(docker-machine env default)"' >> ~/.bash_profile
source ~/.bash_profile

# Nodejs
brew install node
npm update npm -g
```


## <a name="dependencies"></a>System Dependencies

Though not advised, you can skip Docker and develop on your machine directly without containerization. 

- Use this automatic [setup script](https://github.com/kengz/mac_setup) for Ubuntu and MacOSX, 
- run <a href="https://github.com/kengz/aiva/tree/master/bin/install" target="_blank"><code>bin/install</code></a>. 
- run `npm run gi`

These install all the system and project dependencies automatically. There is a lot of components listed there for your inspection.


## <a name="project-dependencies"></a>Project Dependencies

You can save your project dependencies:

- `package.json` for `node.js`
- `requirements.txt` for `python`
- `Gemfile` for `ruby`

and the Docker container autoruns the installation script on every start: `npm run gi` runs the following in sequence:

- `npm install`
- `pip3 install -r requirements.txt`
- `bundle install`

If you're not using Docker, you need to run `npm run gi` manually.


## Custom deployment

All bot deployment commands are wrapped with `npm` inside `package.json`. For more novice users, you can customize the `scripts` in `package.json`. For example, changing "aiva" and "aivadev" to your bot name of choice.


## Different Botname

If you prefer a different bot name, replace "aiva" from the `bin/.keys-` and in `package.json`.

Sometimes you just can't use the bot name across platforms - maybe it's already taken, or they require a "bot" word be added to it. 

For all adapters, `process.env.BOTNAME` defaults to the name after "-" in `.keys-<botname>`. You can change this by providing in there an env variable `<ADAPTER>_BOTNAME`, e.g. `TELEGRAM_BOTNAME=AIVAthebot`.


## Monitoring

AIVA uses [`supervisord`] with Docker, and [`forever`](https://github.com/foreverjs/forever) without Docker. Either way, the bot logs are written to `logs/`.

Additionally, the non-bot logs are written to `/var/log/` for `supervisor`, `nginx` and `neo4j` in Docker.


## <a name="docker-port"></a>Docker port forwarding

In the uncommon case where you need to expose a port from Docker, there are 4 steps:

- EXPOSE the port and its suggorate in `bin/Dockerfile`, then rebuild the Docker image for changes to apply.
- define the reverse proxy for the port following the pattern in `bin/nginx.conf`
- for MacOSX, add the ports for host-container port forwarding by Virtualbox in `bin/start.sh` above `VBoxManage controlvm ...`
- publish the Docker port in `bin/start.sh` at `docker run ... -p <hostport>:<containerport>`


## <a name="ngrok"></a>Webhook using ngrok

You don't need to specify any webhook urls; they are set up automatically in <a href="https://github.com/kengz/aiva/blob/master/index.js#L86" target="_blank"><code>index.js with ngrok</code></a>. Access the ngrok interface at [`http://localhost:4040`](http://localhost:4040) after AIVA is run.

Note that for each adapter, if it needs a webhook, you need to specify the `PORT` and the environment key for the webhook, at the top of <a href="https://github.com/kengz/aiva/blob/master/index.js" target="_blank"><code>index.js</code></a> in variables `adapterPorts`, `adapterWebhookKey`.

```javascript
// js: index.js
// list of all ports used, including for adapters
var portList = {
  production: {
    ngrok: '4040-4041',
    neo4j: 7474,
    socketIO: 6464,
    slack: 8343,
    telegram: 8443,
    fb: 8543
  },
  development: {
    ngrok: '4040-4041',
    neo4j: 7476,
    socketIO: 6466,
    slack: 8345,
    telegram: 8443,
    fb: 8545
  }
}

// process.env.<key> to set webhook for adapter
var adapterWebhookKey = {
  telegram: 'TELEGRAM_WEBHOOK',
  fb: 'FB_WEBHOOK_BASE'
}
```

If the webhook environment key is not specified, then ngrok will assign it a random emphemeral web url. This is especially useful if you wish to specify a Heroku url, or a custom ngrok url. 


>For example, since Facebook takes 10 minutes to update a webhook, we wish to use a persistent webhook url. We leave Telegram to a random url generated by ngrok.

```shell
# bin/.keys-aiva
FB_WEBHOOK=https://aivabot.ngrok.com
# TELEGRAM_WEBHOOK is not net

# ... AIVA is run, Telegram is given a random url
# stdout log
[Wed Jun 01 2016 12:02:33 GMT+0000 (UTC)] INFO telegram webhook url:  https://ddba2b46.ngrok.io at PORT: 8443
[Wed Jun 01 2016 12:02:33 GMT+0000 (UTC)] INFO Deploy bot: AIVAthebot with adapter: telegram
[Wed Jun 01 2016 12:02:33 GMT+0000 (UTC)] INFO fb webhook url:  https://aivadev.ngrok.io at PORT: 8545
[Wed Jun 01 2016 12:02:33 GMT+0000 (UTC)] INFO Deploy bot: aivadev with adapter: fb
```

<aside class="notice">
If you use a custom ngrok domain, be sure to <a href="https://ngrok.com/docs#authtoken" target="_blank">install your authtoken before hand</a>. A ngrok custom url cannot be used twice.
</aside>

<aside class="notice">
Remember to use HTTPS urls.
</aside>


## Neo4j
If it's your first installation of `Neo4j`, the `bin/install` script will reset the default `username:pswd` from `neo4j:neo4j` to `neo4j:0000`, as required for direct usage. This is also consistent with the default `NEO4J_AUTH` in `.env`. If you wish to reset to your own password, use below:

```shell
neo4j start
# change your password
# note the default username:pswd is neo4j:0000
curl -X POST -d "password=YOUR_NEW_PASSWORD" -u neo4j:<OLD_PSWD> http://localhost:7474/user/neo4j/password
```

Access the Neo4j browser GUI at [`http://localhost:7474`](http://localhost:7474)

## SSH Browser-forwarding

If you're hosting Neo4j on a remote machine and want to access its browser GUI on your local machine, connect to it via 

```shell
ssh -L 8080:localhost:7474 <remote_host>
```

Then you can go to `http://localhost:8080/` on your local browser.
