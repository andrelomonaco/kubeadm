We know containers inside the pod already share the network namespace by default which means they technically have the same IP address.

Apart from this, most things, such as other namespaces, are isolated. For example, process namespace. Containers within the same pod run their process namespace and don’t share any information with another container.

And I wanted precisely the opposite of that to run my application.

For this use case, I was crawling through Kubernetes documentation and came across this field called shareProcessName: true.

This feature was added to Kubernetes 1.17

sudo lsns -t pid

