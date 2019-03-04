# quorum-deploy
Create quorum based consortium chain painless, deploy it to Docker-compose & Kubernetes

## Dependencies
* `docker`
* `quorum 2.0.2`

## Installation
~~~shell
git clone https://github.com/CGems/quorum-deploy.git
cd quorum-deploy
docker build -t quorum -f quorum.Dockerfile .
~~~

## Usage

### Docker-compose
Edit `ip.cfg`, enable the config for `docker` and disable the others, then run
~~~shell
./setup.sh && docker-compose up -d
~~~
Now you have a consortium chain and it's explorer running inside docker, you can test them by
~~~shell
geth --exec 'loadScript("examples/contract_pub.js") attach http://0.0.0.0:22001
open http://localhost:5000
~~~

### Production
Similar to minikube, one important additional thing is that you must have a static ip address for `explorer_ip` in `ip.cfg`. For GCP, follow [this](https://cloud.google.com/sdk/gcloud/reference/compute/addresses/create)
In addition, there are several scripts in the `scripts/` directory may help you. To use them, you must `cd scripts`
~~~shell
./attach node-1 # geth attach to node-1
./inspect node-1 # get into container of node-1
./inspect expl mongodb # get into container of mongodb of explore
./pubkey node-1 # get constellation public key of node-1
~~~
For other scripts, just run it and it will do the thing as it's name says.

### Try on testnet
Our consortium chain is now living on the `GCP Kubernetes Engine`, named [consortiumchain](https://console.cloud.google.com/kubernetes/list?project=consortiumchain).
* Consortium chain explorer url:
~~~shell
http://35.229.221.95:5000/
~~~
* Consortium node rpc urls:
~~~shell
# node-1
http://35.229.229.248:8545
# node-2
http://35.201.215.219:8545
# node-3
http://35.189.163.25:8545
~~~
* Loadbalanced nodes' rpc url:
~~~shell
http://35.194.196.250:8545
~~~
* Consortium node public keys:
~~~shell
# node-1
fsOD/f81FUBcO84u4EGkoHx1ymvPhPcUc7qTuiJX6VY=
# node-2
JEpO9gLgPcQM3II8MP4Hzf0mHrS1vcGIao+AWrK1qCA=
# node-3
RHoXgVLqX9KwMvBfi3os6xylk1+XhPslDW4rLge+Hj4=
~~~
You may play with them in this way
* Public contract creation
~~~shell
# deploy a public contract to node-1
geth --exec 'loadScript("examples/contract_pub.js")' attach http://35.229.229.248:8545
~~~
* Private contract creation
~~~shell
# deploy a private contract to node-1, private for node-2
geth --exec 'loadScript("examples/contract_pri.js")' attach http://35.229.229.248:8545
~~~
Now open [http://35.229.221.95:5000/](http://35.229.221.95:5000/) on your web explorer to explorer blocks & transactions.
