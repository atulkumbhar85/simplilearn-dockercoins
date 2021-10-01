# simplilearn-dockercoins
GITHUB_USERNAME=atulkumbhar85
GITHUB_PROJECT=simplilearn-dockercoins
GITHUB_BRANCH=2021-08
GITHUB_RELEASE=test
NODEPORT=80

cd ${HOME}
git clone https://github.com/${GITHUB_USERNAME}/${GITHUB_PROJECT}
cd ${GITHUB_PROJECT}
git pull
git checkout ${GITHUB_BRANCH}
git pull

SERVICE=hasher
sudo docker image build --file ${SERVICE}/Dockerfile --tag ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${SERVICE}/

SERVICE=rng
sudo docker image build --file ${SERVICE}/Dockerfile --tag ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${SERVICE}/

SERVICE=webui
sudo docker image build --file ${SERVICE}/Dockerfile --tag ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${SERVICE}/

SERVICE=worker
sudo docker image build --file ${SERVICE}/Dockerfile --tag ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${SERVICE}/

SERVICE=hasher
sudo docker network create ${SERVICE}

SERVICE=redis
sudo docker network create ${SERVICE}

SERVICE=rng
sudo docker network create ${SERVICE}

CMD=redis-server
ENTRYPOINT=docker-entrypoint.sh
SERVICE=redis
NETWORK=${SERVICE}
WORKDIR=/data/
sudo docker volume create ${SERVICE}
sudo docker container run --cpus 0.010 --detach --entrypoint ${ENTRYPOINT} --memory 10M --name ${SERVICE} --network ${NETWORK} --restart always --volume ${VOULME}:${WORKDIR}:rw --workdir ${WORKDIR} library/redis:alpine ${CMD}
sudo docker container logs ${SERVICE}
sudo docker container stats --no-stream ${SERVICE}
sudo docker container top ${SERVICE}

CMD=hasher.rb
ENTRYPOINT=ruby
SERVICE=hasher
NETWORK=${SERVICE}
VOULME=${PWD}/${SERVICE}
WORKDIR=/${SERVICE}/
sudo docker container run --detach --entrypoint ${ENTRYPOINT} --name ${SERVICE} --network ${NETWORK} --restart always --volume ${VOULME}/:${WORKDIR}:ro --workdir ${WORKDIR} ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${CMD}
sudo docker container logs ${SERVICE}
sudo docker container stats --no-stream ${SERVICE}
sudo docker container top ${SERVICE}


CMD=rng.py
ENTRYPOINT=python
SERVICE=rng
NETWORK=${SERVICE}
VOULME=${PWD}/${SERVICE}
WORKDIR=/${SERVICE}/
sudo docker container run --detach --entrypoint ${ENTRYPOINT} --name ${SERVICE} --network ${NETWORK} --restart always --volume ${VOULME}/:${WORKDIR}:ro --workdir ${WORKDIR} ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${CMD}
sudo docker container logs ${SERVICE}
sudo docker container stats --no-stream ${SERVICE}
sudo docker container top ${SERVICE}


CMD=worker.py
ENTRYPOINT=python
SERVICE=worker
NETWORK=redis
VOULME=${PWD}/${SERVICE}
WORKDIR=/${SERVICE}/
sudo docker container run --detach --entrypoint ${ENTRYPOINT} --name ${SERVICE} --network ${NETWORK} --restart always --volume ${VOULME}/:${WORKDIR}:ro --workdir ${WORKDIR} ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${CMD}
sudo docker container logs ${SERVICE}
sudo docker container stats --no-stream ${SERVICE}
sudo docker container top ${SERVICE}


NETWORK=hasher
sudo docker network connect ${NETWORK} ${SERVICE}

NETWORK=rng
sudo docker network connect ${NETWORK} ${SERVICE}

CMD=webui.js
ENTRYPOINT=node
SERVICE=webui
NETWORK=redis
VOULME=${PWD}/${SERVICE}
WORKDIR=/${SERVICE}/
sudo docker container run --detach --entrypoint ${ENTRYPOINT} --name ${SERVICE} --network ${NETWORK} --publish ${NODEPORT}:8080--restart always --volume ${VOULME}/:${WORKDIR}:ro --workdir ${WORKDIR} ${GITHUB_USERNAME}/${GITHUB_PROJECT}:${GITHUB_RELEASE}-${SERVICE} ${CMD}
sudo docker container logs ${SERVICE}
sudo docker container stats --no-stream ${SERVICE}
sudo docker container top ${SERVICE}
