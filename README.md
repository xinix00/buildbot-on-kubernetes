# Buildbot on Kubernetes

Not sure what this is? There's a [blogpost][post] that might give some context. In sort, this repo contains all the stuff you should need to deploy buildbot on a kuberentes cluster.

Before getting started you may want to get aquainted with the [Buildbot Documentation][buildbot-docs] and specifically their [Buildbot on Docker-compose][buildbot-compose] tutorial.

## Getting started

0. Get a kubernetes cluster up and running. For testing purposes, I suggest [minikube][minikube-setup].
1. Setup your local volumes.

```
$ kubectl create -f simple/volumes.yaml
<output>
```

**Warning:** For a long-term maintainable cluster you should consider using [NFS][k8s-nfs] or some other backend for your storage.

2. Setup the kubernetes database.

```
$ kubectl create -f simple/postgres.yaml
<output>
```

3. Start the kubernetes master.

```
$ kubectl create -f simple/master.yaml
<output>
```

4. Start the kubernetes worker.

```
$ kubectl create -f simple/worker.yaml
<output>
```

**Note:** This setup uses local volumes which are probably owned by the `root` user on your cluster. You'll most likely need to change the permissions on the directories being used so the buildbot worker can use it to store data.

```
$ minikube ssh
$ sudo chown -R 1000:1000 /data/pv-1 /data/pv-2 /data/pv-3
```

**NOTE** This step may fail as `/data/pv-1`, `/data/pv-2`, or `/data/pv-3` haven't been created yet.
You may need to repeat this step after testing your buildbot cluster out a bit after step 5 below.

If you decide to use something else like [NFS][k8s-nfs] or [Glusterfs][k8s-glusterfs] this step may not be necessary as you may be able to specify the permissions of the block device as in a kubernetes configuration file.

5. Check out your cluster locally with:

```
$ kubectl get pods -l app=buildbot -l tier=master -o template --template="{{range.items}}{{.metadata.name}}{{end}}" | xargs -I{} kubectl port-forward {} 8080
```

6. Test out your cluster! Go to http://localhost:8080/#/builders/ and force a new build.

7. (Optional) Fork this repository and customize it to your needs. All of the necessary buildbot configuration file can be found and modified for your project's needs.

For something a bit more complicated take a look at the configurtion in the `robust/` directory.

## Contributing

If feedback or a fix, that's great! Create an [Issue][submit-issue] or a [Pull Request][pr]. If you choose the latter, this is a good workflow to follow:

1. Fork the repository.
2. Make whatever changes you want on your branch.
3. Create a pull request from `your_remote:your_branch` to `ElijahCaine:master`.

I'll give feedback and once everything looks good we'll squash the commits and merge them in.

## Origin

The Kubernetes setup outlined here is almost a 1:1 mapping with the configuration in `docker-compose/simple/`.
If you're up for a challenge try implementing the setup in `docker-compose/multimaster/`!

## Licensing

This repository is a fork a repo from the buildbot org ([upstream][upstream]) which wasn't licensed.
The additions I've made are licensed under [MIT][license], Elijah C. Voigt.

Happy hacking!

[buidlbot-docs]: https://docs.buildbot.net/current/index.html
[buildbot-compose]: https://docs.buildbot.net/current/tutorial/docker.html
[k8s-glusterfs]: http://kubernetes.io/docs/user-guide/volumes/#glusterfs
[k8s-nfs]: http://kubernetes.io/docs/user-guide/volumes/#nfs
[minikube-setup]: http://kubernetes.io/docs/getting-started-guides/minikube/
[post]: #
[pr]: https://github.com/ElijahCaine/buildbot-on-kubernetes/pulls
[submit-issue]: https://github.com/ElijahCaine/buildbot-on-kubernetes/issues
[upstream]: https://github.com/buildbot/buildbot-docker-example-config
[license]: http://choosealicense.com/licenses/mit/
