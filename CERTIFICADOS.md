# Certificados em um cluster Kubernetes

Certificados sao utilizados em um cluster Kubernetes para permir a autenticacao e a seguranca da comunicao entre diversos componentes
de um cluster Kubernetes.

Lembrando que estes componentes nao necessariamente estao no mesmo node, tendo que utilizar a rede externa para esta comunicacao.

![image](https://github.com/andrelomonaco/kubeadm/assets/48954728/0c64adf6-c9b1-41aa-ae22-172839687455)


# Cluster CA

- Cluster Certificate Authority is the trusted root for the entire cluster
- All cluster certificates are signed by the Cluster CA
- Used by components to validate API Server, etc 


# Links 

All You Need to Know About Certificates in Kubernetes
https://www.youtube.com/watch?v=gXz4cq3PKdg

Kubernetes Components
https://kubernetes.io/docs/concepts/overview/components

PKI certificates and requirements
https://kubernetes.io/docs/setup/best-practices/certificates
