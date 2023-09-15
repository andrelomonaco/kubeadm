
A namespace is a kernel feature that can isolate some resources for a group of processes. For example, such resources are the process ID number space, users, network interfaces, etc.



We know containers inside the pod already share the network namespace by default which means they technically have the same IP address.
Apart from this, most things, such as other namespaces, are isolated. For example, process namespace. Containers within the same pod run their process namespace and don’t share any information with another container.
And I wanted precisely the opposite of that to run my application.
For this use case, I was crawling through Kubernetes documentation and came across this field called shareProcessName: true.
This feature was added to Kubernetes 1.17

sudo lsns -t pid

# Links

Host PID of a Process Running in a Docker Container
https://www.baeldung.com/linux/docker-container-process-host-pid

Share Process Namespace between Containers in a Pod
https://kubernetes.io/docs/tasks/configure-pod-container/share-process-namespace/

Share process namespace among containers in a Kubernetes Pod
https://medium.com/surajincloud/share-process-namespace-among-containers-in-a-kubernetes-pod-59eefa449f1a

What have containers done for you lately?
https://www.youtube.com/watch?v=MHv6cWjvQjM

