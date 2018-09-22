# How to set up Alf.io on OpenShift

You'll either need to [locally install minishift](https://docs.openshift.org/latest/minishift/index.html) 
(great for testing! perhaps slightly increase resources from the default via `minishift config set memory 4096; minishift config set cpus 4`),
or need to create at least a FREE TRIAL ;-) account on [openshift.com](https://www.openshift.com).
Either way you'll use the OC CLI, and then thanks to the configuration in alf.io/.s2i/ can simply do:

_TODO How to fully automate all this?_

    oc new-project alf-io

_TODO How to give the service a better name than "postgresql-95-centos7" ?

_TODO How to use a secret for the password?_

    oc new-app \
        -e POSTGRESQL_USER=alfy \
        -e POSTGRESQL_PASSWORD=237vnaw8zevbaacwzebt \
        -e POSTGRESQL_DATABASE=alfio \
        centos/postgresql-95-centos7

_TODO How to do this from the CLI?_ Use the OpenShift UI: Storage > Create Storage "alf.io-db".

_TODO How to do this from the CLI?_ Use the OpenShift UI: Applications > Deplyoment > postgres > Pause Rollouts,
Configuration: Remove the default empty dir volume,  Add storage, Storage = "alf.io-db" (create above) & Mount Path = /var/lib/pgsql/data,
unpause rollouts.

    oc new-app fabric8/s2i-java~https://github.com/vorburger/alf.io#openshift \
        -e POSTGRES_ENV_POSTGRES_USERNAME=alfy \
        -e POSTGRES_ENV_POSTGRES_PASSWORD=237vnaw8zevbaacwzebt \
        -e POSTGRES_ENV_POSTGRES_DB=alfio \
        -e POSTGRES_PORT_5432_TCP_ADDR=postgresql-95-centos7 \
        -e POSTGRES_PORT_5432_TCP_PORT=5432

    oc expose svc/alfio

    oc logs -f bc/alfio

    oc status

_TODO How to avoid having to do this??_ Use the OpenShift UI: Applications > Deployment > alfio > Edit YAML,
add a `containerPort: 8080` (like the existing 8778 & 9779), and then in Applications > Services > alfio > Edit YAML,
add an additional `port` etc. for 8080, and in Applications > Routes > alfio > Edit YAML
and change the from `targetPort: 8778-tcp` to `targetPort: 8080-tcp`.

Now open e.g. `http://alfio-alf-io.192.168.42.182.nip.io/` ...

_TODO Something is NOK with the 8080 port... auto-forwarding to 8443... see https://github.com/alfio-event/alf.io/issues/511 :-(_

_TODO How to add a healthcheck to Postgres._
_TODO How to have monitoring metrics for Postgres._


## Use s2i locally for testing, without OpenShift, just a local Docker

    s2i build --copy . fabric8/s2i-java alf.io
    docker run -p 8080:8080 alf.io

_TODO `--incremental=true` how-to?_



## Background

This also required some work in an S2I Java Builder to be smooth, see:

* https://github.com/fabric8io-images/s2i/issues/118
* https://github.com/fabric8io-images/s2i/issues/113
* https://github.com/fabric8io-images/s2i/issues/115
* https://github.com/vorburger/s2i-java-example/tree/gradle

Postgres container:

* https://docs.okd.io/latest/using_images/db_images/postgresql.html
* https://github.com/sclorg/postgresql-container
