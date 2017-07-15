# Buildbot on Kubernetes on GCE

Buildbot on Kubernetes on Google Container Engine, using volume stores as - Google Compute Engine - disks.

Goals:
- Use sqlLite, since it's good enough
- Use volumes mounted to seperated disks of GCE (so your data will remain)
- Create a single master and multiple slaves setup 

## Getting started
0. Create GCE Disks:
- buildbot-master-disk
- buildbot-worker-disk (this one will be removed)

1. Setup your local volumes.

```
$ kubectl create -f simple/volumes.yaml
<output>
```

2. Start the buildbot master.

```
$ kubectl create -f simple/master.yaml
<output>
```

3. Start the buildbot worker.

```
$ kubectl create -f simple/worker.yaml
<output>
```

4. Check out your cluster locally with:

```
$ kubectl get pods -l app=buildbot -l tier=master -o template --template="{{range.items}}{{.metadata.name}}{{end}}" | xargs -I{} kubectl port-forward {} 8080
```

5. Test out your cluster! Go to http://localhost:8080/#/builders/ and force a new build.

6. (Optional) Fork this repository and customize it to your needs. All of the necessary buildbot configuration file can be found and modified for your project's needs.

## Customized docker files
buildbot master based on buildbot/buildbot-master including Slack plugin
- See docker/buildbot-master
buildbot worker based on Alpine
- See docker/buildbot-worker

## Contributing

If feedback or a fix, that's great! Create an [Issue][submit-issue] or a [Pull Request][pr]. If you choose the latter, this is a good workflow to follow:

1. Fork the repository.
2. Make whatever changes you want on your branch.
3. Create a pull request from `your_remote:your_branch` to `XiniX00:master`.

I'll give feedback and once everything looks good we'll squash the commits and merge them in.

## Origin

The Kubernetes setup outlined here is almost a 1:1 mapping with the configuration in `docker-compose/simple/`.
If you're up for a challenge try implementing the setup in `docker-compose/multimaster/`!

## Licensing

This repository is a fork a repo from the buildbot org ([upstream][upstream]) which was licensed under [MIT][license], Elijah C. Voigt.
The additions I've made are licensed under [MIT][license].

Happy hacking!

[buidlbot-docs]: https://docs.buildbot.net/current/index.html
[buildbot-compose]: https://docs.buildbot.net/current/tutorial/docker.html
[k8s-glusterfs]: http://kubernetes.io/docs/user-guide/volumes/#glusterfs
[k8s-nfs]: http://kubernetes.io/docs/user-guide/volumes/#nfs
[minikube-setup]: http://kubernetes.io/docs/getting-started-guides/minikube/
[pr]: https://github.com/XiniX00/buildbot-on-kubernetes/pulls
[submit-issue]: https://github.com/XiniX00/buildbot-on-kubernetes/issues
[upstream]: https://github.com/ElijahCaine/buildbot-on-kubernetes
[license]: http://choosealicense.com/licenses/mit/