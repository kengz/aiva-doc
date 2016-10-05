# <a name="setup-tips"></a>Setup tips

## <a name="docker-installation"></a>Docker installation

[Docker](https://www.docker.com/) is a nice way to package and distribute complex modules, it also allows you to develop in safe isolated environment with containerization. 

The [AIVA Docker image](https://hub.docker.com/r/kengz/aiva/) (1Gb) comes ready-to-run for the repo source code, same as if ran locally.

### Ubuntu

- Digital Ocean: The easiest, using their [Docker 1-click app on Ubuntu](https://www.digitalocean.com/features/one-click-apps/docker/). Docker comes installed with it.
- Ubuntu from scratch: See [this guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04) by Digital Ocean.

Then, you need `nodejs` to run basic setup for entering Docker.

```shell
# Nodejs
curl -sL https://deb.nodesource.com/setup_6.x | bash -
sudo apt-get install -y nodejs
```

If your Digital Ocean instance has insufficient swap memory, boost it:

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


## Custom deployment


All bot deployment commands are wrapped with `npm` inside `package.json`. For more novice users, you can customize the `scripts` in `package.json`. For example, changing "aiva" and "aivadev" to your bot name of choice.


## Monitoring

AIVA uses [`supervisord`] with Docker, and [`forever`](https://github.com/foreverjs/forever) without Docker. Either way, the bot logs are written to `logs/`.

Additionally, the non-bot logs are written to `/var/log/` for `supervisor`, `nginx` and `neo4j` in Docker.


## <a name="dockerization"></a>Dockerization


<aside class="notice">
Advanced users can use this section to configure and extend AIVA.
</aside>

With some spirit of try-hard devops, we package the Docker image so it follows common deployment practices. Below are the primary processes that are ran in the Docker container, and the relevant config files. They package the original AIVA that is ran on a local machine into Docker.

- `supervisord`, with `bin/supervisord.conf`: The main entry-point process that runs everything else. Logs output to the main bash session started by `npm start`
- `nginx`, with `bin/nginx.conf`: Helps expose the Docker port to the host machine and the outside world. See [Docker Port-forwarding](#docker-port) for how it's done.

Helper bash scripts for running [Commands](#commands);

- `bin/start.sh` ran by `npm start`: start a container in `production` or `development` environment, with the primary bash session to use `supervisord` only.
- `bin/enter.sh` ran by `npm run enter`: start a parallel non-primary bash session to enter the container.
- `bin/stop.sh` ran by `npm stop`: stop a container and the bot inside.
- `bin/reset.sh` ran by `npm run reset`: stop and remove a container, so you can create a fresh one with `npm start` if shit goes wrong.


## <a name="docker-port"></a>Docker Port-forwarding

In the uncommon case where you need to expose a port from Docker, there are 4 steps:

- EXPOSE the port and its suggorate in `bin/Dockerfile`, then rebuild the Docker image for changes to apply.
- define the reverse proxy for the port following the pattern in `bin/nginx.conf`
- for MacOSX, add the ports for host-container port forwarding by Virtualbox in `bin/start.sh` above `VBoxManage controlvm ...`
- publish the Docker port in `bin/start.sh` at `docker run ... -p <hostport>:<containerport>`


## <a name="ngrok"></a>Webhook using ngrok

You don't need to specify any webhook urls; they are set up automatically in <a href="https://github.com/kengz/aiva/blob/master/index.js#L86" target="_blank"><code>index.js with ngrok</code></a>. Access the ngrok interface at [`http://localhost:4040`](http://localhost:4040) (production) or [`http://localhost:4041`](http://localhost:4041) (development) after AIVA is run.

Note that for each adapter, if it needs a webhook, you need to specify the `PORT` and the environment key for the webhook, from `config/`.


If the webhook environment key is not specified, then ngrok will assign it a random emphemeral web url. This is especially useful if you wish to specify a Heroku url, or a custom ngrok url. 


>For example, since Facebook takes 10 minutes to update a webhook, we wish to use a persistent webhook url. We leave Telegram to a random url generated by ngrok.

```shell
# FB_WEBHOOK is set as https://aivabot.ngrok.com
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


## SSH Browser-forwarding

If you're hosting Neo4j on a remote machine and want to access its browser GUI on your local machine, connect to it via 

```shell
ssh -L 8080:localhost:7474 <remote_host>
```

Then you can go to `http://localhost:8080/` on your local browser.
