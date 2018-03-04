# How to set up Alf.io on OpenShift

You'll either need to [locally install minishift](https://docs.openshift.org/latest/minishift/index.html) (great for testing!),
or need to create at least a FREE TRIAL ;-) account on [openshift.com](https://www.openshift.com).
Either way you'll the OC CLI, and then thanks to the configuration in alf.io/.s2i/ can simply do:

    TODO...


## Use s2i locally for testing, without OpenShift, just a local Docker

    s2i build --copy . fabric8/s2i-java alf.io
    docker run -p 8080:8080 alf.io

_TODO `--incremental=true` how-to?_

_TODO Postgres container, and data-source how-to?_


## Background

This content originally appeared on http://blog2.vorburger.ch.

It required some work in an S2I Java Builder to be smooth, see:

* https://github.com/fabric8io-images/s2i/issues/118
* https://github.com/fabric8io-images/s2i/issues/113
* https://github.com/fabric8io-images/s2i/issues/115
* https://github.com/vorburger/s2i-java-example/tree/gradle
