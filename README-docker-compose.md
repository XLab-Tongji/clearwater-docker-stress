
# Clearwater Docker

This repository contains instructions and resources for deploying [Project Clearwater](http://www.projectclearwater.org) as [Docker](https://www.docker.com/) containers.  It describes three deployment options -- the first two are recommended.   

- Using [Docker Compose](https://docs.docker.com/compose/) for quick deployments on a single host.


## Using Compose

There is a [Compose file](minimal-distributed.yaml) to instantiate a minimal (non-fault-tolerant) distributed Clearwater deployment under Docker.
  - 104.155.15.20

#### Preparation

To prepare your system to deploy Clearwater using Compose, after running the common preparation steps above, run:

    # Install Docker Compose (on Ubuntu).
    sudo apt-get install python-pip -y
    sudo pip install -U docker-compose

    # Build the base Clearwater docker image.
    cd clearwater-docker
    sudo docker build -t clearwater/base base

#### Starting Clearwater

To start the Clearwater services, run:

    # Build all the other Clearwater Docker images and start a deployment.
    sudo docker-compose -f minimal-distributed.yaml up -d
    # Check the installing status
    sudo docker ps -a

#### Scaling the deployment

Having started up a deployment, it is then possible to scale it by adding or removing additional nodes.  E.g. run

    sudo docker-compose -f minimal-distributed.yaml scale sprout=2 astaire=2 chronos=2 cassandra=2

Note that it is not possible to scale *down* storage node clusters -- see limitations above.

If you scale *up* the clusters of storage nodes, you can monitor progress as new nodes join the clusters by running `utils/show_cluster_state.sh`.

#### Run the stress testing in the stress container

Referring to [the clearwater-sip-stress handbook](http://clearwater.readthedocs.io/en/stable/Clearwater_stress_testing.html), let say the stress container ID is **STRESS_CONATNER_ID**,and then run:

    # Enter into the stress container.
    sudo docker exec -it STRESS_CONATNER_ID /bin/bash
    /usr/share/clearwater/bin/run_stress <home_domain> <number of subscribers> <duration in minutes>
