---           
layout: post
title: Command Line Tips And Tricks To View Container Logs in Kubernetes
date: 2023-11-03 00:00:00 IST
comments: true
categories: techops kubernetes how-to
---

There are different ways to view logs from containers running inside Kubernetes. If you have a log agent that pushes logs
to a central location like Elasticsearch, you can view them from Kibana. But for debugging and quick views, sometimes it's much 
easier to use kubectl. In this article, I will describe the kubectl options to view Kubernetes container logs. I'll also 
explore a way to split stdout and stderr output.

### Basic Log Retrieval

The standard kubectl command has many cool flags when it comes to log viewing.
```sh
kubectl -n web logs nginx-849786f6c4-g5vh6
```

This will fetch you the logs from the pod. It will dump the current log file from the beginning.

kubectl returns only the latest log file. If the log file has been rotated on the node running the pod, you won't 
see older log entries.

The above command assumes the pod has only one container. For multi-container pods, you need to specify the container name
```sh
kubectl -n web logs nginx-849786f6c4-g5vh6 -c sidecar
```

Or if you want logs from all the containers
```sh
kubectl -n web logs nginx-849786f6c4-g5vh6 --all-containers
```
You can fetch logs from all pods in a Deployment or a StatefulSet at once by using label selectors.
```sh
kubectl -n web logs -l service=comments -l tier=web
```
When using selectors you might have to add
```sh
--max-log-requests=<number>
```
if you have more than 5 (the default) log-producing containers that match the selectors. 
Set the number to at least the number of your log producers, or something sufficiently high.

You can mix and match these options, e.g., to fetch logs from the “sidecar” containers only from a specific pod type
```sh
kubectl -n web logs -l service=comments -l tier=web -c sidecar
```

### Streaming Logs

The `-f` option lets you do the same thing that `tail -f` does on Unix/Linux. 
You can use it in conjunction with the previous options too.

### Log Filters
You can fetch the logs over a given period
```sh
--since=1h
```
Or since a specific timestamp in RFC 3339 format
```sh
--since-time=2023-11-02T13:58:00Z
```
Or by the last n lines
```sh
--tail=100
```
Or by the number of bytes
```sh
--limit-bytes=1048576
```


### Viewing Logs From Previous Runs
Pods come and go, so if you want to view the logs from a previous run of the pod, use
```sh
kubectl -n web logs -p -c sidecar nginx-849786f6c4-g5vh6
```

### Splitting stdout And stderr

Kubernetes collects the standard output and error streams from containers and exposes them through its API.
`kubectl logs` talks to that API. You might want to view the stdout and stderr logs separately for a container - 
but currently, this is not possible. This feature is [in the works](https://github.com/kubernetes/enhancements/pull/3289){:target="_blank"}
as a Kubernetes Enhancement Proposal (KEP). The KEP is merged but not implemented as of this writing. 
Until then, you can use a workaround to split the streams. I describe it below.

#### Using Sidecar Logging Containers To Split Streams
A sidecar is a container in a pod that performs a specific action only. E.g. collecting logs from the main container 
and sending them to a remote logging server. If you can change your main container to write out and err streams to
different log files, then you can use sidecars to split the streams for kubectl viewing later.

The sidecars will access the main container's logs through a shared volume. Containers in the same pod do not share a 
filesystem, so you have to use a shared volume. There are different ways to do this - in this example, we will use 
an emptyDir. An emptyDir’s contents are erased when the pod is removed - either deleted or moved to another node.

![Pod with an application container and two sidecars for splitting output streams](/assets/images/kubernetes-logs-sidecar.png)

- Configure your main app container to write stdout to one log file and stderr to another in the shared volume.
- Add two sidecar containers to the pod with the same shared volume.
- One sidecar container will read from the stdout log file and write it to its (sidecar) stdout.
- The other sidecar container will read from the stderr log file and write it to its (sidecar) stdout.

Note that in this case, the main container does not produce anything to stdout or stderr.
You can try out [this example](https://github.com/talonx/kubernetes-playground/blob/main/descriptors/pod-two-stream-sidecars.yaml){:target="_blank"} yourself.

To tail all stdout output from this deployment
```sh
kubectl -n web logs -f -l service=comments -c stdout-sidecar
```
To tail all stderr output from a specific pod
```sh
kubectl -n web logs -f comment-web-849786f6c4-g5vh6 -c stderr-sidecar
```

I’ve tried to summarize the most useful options for `kubectl logs` here, along with a way to split the out and err streams for a container.

Know more useful tips? Let me know here or on Twitter [@talonx](https://twitter.com/talonx){:target="_blank"}.


