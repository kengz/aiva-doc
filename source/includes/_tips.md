# <a name="setup-tips"></a>Setup tips

## Neo4j
If it's your first installation of `Neo4j`, change the password:

```shell
neo4j start
# change your password
# note the default username:pswd is neo4j:neo4j
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

All the system dependencies are installed automatically by running `npm run gi`, which executes [`bin/install`](./bin/install). If you'd like to setup your VM manually, see/edit the setup script; below is the list:

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

