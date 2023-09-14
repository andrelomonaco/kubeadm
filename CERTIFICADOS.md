# Certificados em um cluster Kubernetes

Certificados sao utilizados em um cluster Kubernetes para permir a autenticacao e a seguranca da comunicao entre diversos componentes
de um cluster Kubernetes.

Lembrando que estes componentes nao necessariamente estao no mesmo node, tendo que utilizar a rede externa para esta comunicacao.

Por padrao kubeadm cria todos os certificados necessarios para a implementacao de um cluster, mas e possivel fornecer os proprios certificados durante a instalacao.

![image](https://github.com/andrelomonaco/kubeadm/assets/48954728/0c64adf6-c9b1-41aa-ae22-172839687455)

Por padrao os certificados sao criados com expiracao de 1 ano, porem ao se utilizar o kubeadm para uma atualizacao os certificados sao renovados automaticamente.

Para verificar a expiracao dos certificados utilizar o seguinte comando

```
sudo kubeadm certs check-expiration

CERTIFICATE                EXPIRES                  RESIDUAL TIME   CERTIFICATE AUTHORITY   EXTERNALLY MANAGED
admin.conf                 Sep 10, 2024 16:33 UTC   362d            ca                      no
apiserver                  Sep 10, 2024 16:33 UTC   362d            ca                      no
apiserver-etcd-client      Sep 10, 2024 16:33 UTC   362d            etcd-ca                 no
apiserver-kubelet-client   Sep 10, 2024 16:33 UTC   362d            ca                      no
controller-manager.conf    Sep 10, 2024 16:33 UTC   362d            ca                      no
etcd-healthcheck-client    Sep 10, 2024 16:33 UTC   362d            etcd-ca                 no
etcd-peer                  Sep 10, 2024 16:33 UTC   362d            etcd-ca                 no
etcd-server                Sep 10, 2024 16:33 UTC   362d            etcd-ca                 no
front-proxy-client         Sep 10, 2024 16:33 UTC   362d            front-proxy-ca          no
scheduler.conf             Sep 10, 2024 16:33 UTC   362d            ca                      no

CERTIFICATE AUTHORITY   EXPIRES                  RESIDUAL TIME   EXTERNALLY MANAGED
ca                      Sep 08, 2033 16:33 UTC   9y              no
etcd-ca                 Sep 08, 2033 16:33 UTC   9y              no
front-proxy-ca          Sep 08, 2033 16:33 UTC   9y              no

```

# Cluster CA

- Cluster Certificate Authority is the trusted root for the entire cluster
- All cluster certificates are signed by the Cluster CA
- Used by components to validate API Server, etc 

# API Server HTTPS

- Serving certificate and key are required for HTTPS
- Serving certificate is signed by Cluster CA
- Componentes authenticate the API server
- Configured using --tls-cert-file and --tls-private-key-file flags

# HA Considerations

- Multiple API servers must be fronted with a load balancer
- Each master has its own certificate
- Load balancer's DNS name and IP address should be part of the certificate's
- Subject Alternative Name (SAN) field

# Kubelet HTTPS

- The Kubelet exposes an API over HTTPS
- Consumed by API server when getting logs, metrics, exec, etc
- Serving certificate and key are required for HTTPS
- Certificate is signed by Cluster CA
- API server authenticates the Kubelet

- Access to the Kubelet API is protected by authentication and authorization
- The Kubelet authenticates clients using client certificates
- API server has a Kubelet client certificate that is signed by Cluster CA

# X.590 Client Cert Authentication

Each Kubernetes core componenet has its own client certificate


| Component | Common Name | Organizations |
| --- | --- | --- |
| Controller Manager | system:kube-controller-manager |
| Scheduller | system:kube-scheduler |
| Kube Proxy | system:kube-proxy |
| Kubelet | system:node:${hostname} | system:nodes |

# Kubelet Client Certificates

Each Kubelet on the cluster has its own identity
Achieved by having Kubelet-specific client certificates
Enable the use of the Node Auhtorizes and Node Restriction Admission Plugin
Limit Kubelet read and write access to resources that are related to the node itsefl and pods bound to the node

Kubelet can request a new client certificate when the current one is nearing expiration

# Kubelet Cert Bootstrapping

The Kubelet needs a client certificate to access the API server
It also needs a serving certificate for its own API
Instead of the admin having to generate certificates for each Kubelet, the Kubelet can request certificates
as it start up
Built on top of the Certificates API and Boostrap token authenticator

# Certificate Revocation Lists

Kubernetes does not currently support CRLs
Can use RBAC to "revoke" them

# Ingress

TLS can be configured for services exposed during Ingress
Define a secret with a certificate and private key, and reference in in the ingress resource
cert-manager: Auto cert generation using Let's Encrypt for Ingress

# Certificates Resume


| Component | Certificate | Purpose | Localization |
| --- | --- | ---- | --- |
| API server | Cluster CA | Authenticate clients, TLS | Verify |
| API server | ETCD CA | ETCD Server Authentication | Verify |
| API server | ETCD Client cert | ETCD Client Authentication | Verify |
| API server | Serving certificate | Serving API over HTTPS |  Verify |
| API Server | Kubelet client cert | Authentication against Kubelet | Verify |
| Controller Manager | Client certificate | Authentication against API Server | Verify |
| Scheduler | Client certificate | Authentication against API Server | Verify |
| Kubelet | Serving certificate | Serving API over HTTPS | Verify |
| Kubelet | Client certificate | Authentication against API Server | Verify |
| Kubelet | Cluster CA | Authentication clients | Verify |
| Kube Proxy | Client certificate | Authentication against API server | Verify |


# Links 

All You Need to Know About Certificates in Kubernetes
https://www.youtube.com/watch?v=gXz4cq3PKdg

Kubernetes Components
https://kubernetes.io/docs/concepts/overview/components

PKI certificates and requirements
https://kubernetes.io/docs/setup/best-practices/certificates

TLS bootstrapping
https://kubernetes.io/docs/reference/access-authn-authz/kubelet-tls-bootstrapping/

cert-manager (previous kube-lego)
https://cert-manager.io/
