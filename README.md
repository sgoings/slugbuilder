# Deis Slugbuilder v2

[![Build Status](https://travis-ci.org/deis/slugbuilder.svg?branch=master)](https://travis-ci.org/deis/slugbuilder)

Deis (pronounced DAY-iss) is an open source PaaS that makes it easy to deploy and manage
applications on your own servers. Deis builds on [Kubernetes](http://kubernetes.io/) to provide
a lightweight, [Heroku-inspired](http://heroku.com) workflow.

## Work in Progress

![Deis Graphic](https://s3-us-west-2.amazonaws.com/get-deis/deis-graphic-small.png)

Deis Slugbuilder v2 is changing quickly. Your feedback and participation are more than welcome, but be
aware that this project is considered a work in progress.

# About

Deis Slugbuilder takes a git archive and compiles a slug and puts it in endpoint which can be a S3 based object store or file server. It's launched by [`deis/builder`] containers when procfile-based application code is `git push`ed. Please see https://github.com/deis/builder/blob/master/README.md for a more detailed description of how `slugbuilder` interacts with `builder`.

# Hacking Slugbuilder

First, install [helm](http://helm.sh) and [boot up a kubernetes cluster][install-k8s]. Next, add the
`deis` repository to your chart list:

```console
$ helm repo add deis https://github.com/deis/charts
```

Then, install the Deis chart!

```console
$ helm install deis/deis
```

The chart will install the entire Deis platform onto Kubernetes. You can monitor all the pods that it installs by running:

```console
$ kubectl get pods --namespace=deis
```

Once this is done, SSH into a Kubernetes minion, and run the following:

```
$ curl -sSL http://deis.io/deis-cli/install.sh | sh
$ sudo mv deis /bin
$ kubectl get service deis-workflow
$ deis register 10.247.59.157 # or the appropriate CLUSTER_IP
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
$ eval $(ssh-agent) && ssh-add ~/.ssh/id_rsa
$ deis keys:add ~/.ssh/id_rsa.pub
$ deis create --no-remote
Creating Application... done, created madras-radiator
$ deis pull deis/example-go -a madras-radiator
Creating build... ..o
```

If you want to hack on a new feature, rebuild your code, build the `deis/slugbuilder` image and push it to a Docker registry. The `$DEIS_REGISTRY` environment variable must point to a registry accessible to your Kubernetes cluster. If you're using a locally hosted Docker registry, you may need to configure the Docker engines on your Kubernetes nodes to allow `--insecure-registry 192.168.0.0/16` (or the appropriate address range).

```console
$ make build docker-build docker-push
```

## License

Copyright 2013, 2014, 2015 Engine Yard, Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at <http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.


[install-k8s]: http://kubernetes.io/gettingstarted/
