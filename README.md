
# Clearwater Docker

This repository contains instructions and resources for deploying [Project Clearwater](http://www.projectclearwater.org) as [Docker](https://www.docker.com/) containers.  It describes three deployment options -- the first two are recommended.   

- Using [Docker Compose](https://docs.docker.com/compose/) for quick deployments on a single host.


## Using Compose

There is a [Compose file](minimal-distributed.yaml) to instantiate a minimal (non-fault-tolerant) distributed Clearwater deployment under Docker.

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

#### Bulk-Provisioning Numbers

If you are provisioning numbers for SIPp stress testing, we've produced a simple process for this - just run the following script on the Cassandra container node:

    /usr/share/clearwater/crest-prov/src/metaswitch/crest/tools/stress_provision.sh
This provisions numbers starting at 201000000 with the password 7kkzTyGW, and will provision 50,000 users by default, which takes around 15 minutes - to provision a different number, just pass it in as an argument:

    /usr/share/clearwater/crest-prov/src/metaswitch/crest/tools/stress_provision.sh 200000

This will only provision data into Homestead's call-path cache, for efficiency - if you need Homer, Memento or Homestead-prov provisioning, follow the process below.


*Note:* before run the stress_provision.sh, please make sure the cassandra-cli is working, you could have a test by running:

    cassandra-cli -v

if it not working, then check the cassandra service by running:

    netstat -nltp

the port 9160 should be listening if the cassandra service is running, otherwise, run the following bash:

    /etc/init.d/cassandra start

#### Run the stress testing in the stress container

Referring to [the clearwater-sip-stress handbook](http://clearwater.readthedocs.io/en/stable/Clearwater_stress_testing.html), let say the stress container ID is **STRESS_CONATNER_ID**,and then run:

    # Enter into the stress container.
    sudo docker exec -it STRESS_CONATNER_ID /bin/bash
    /usr/share/clearwater/bin/run_stress <home_domain> <number of subscribers> <duration in minutes>
