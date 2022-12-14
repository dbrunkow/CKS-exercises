# Cluster Hardening (15%)

* Restrict access to Kubernetes API

* Use Role Based Access Controls to minimize exposure

* Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones.

* Update Kubernetes frequently

## References
github.com > cncf> curriculum> [CKS Curriculum](https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.23.pdf)

kubernetes.io > Documentation > Reference > kubectl CLI > [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

https://kubernetes.io/docs/reference/access-authn-authz/authentication

https://kubernetes.io/docs/reference/access-authn-authz/authorization/

## Restrict access to Kubernetes API
### Questions
#### Setup token file access to kube-apiserver

token.csv
```
8j79724ja8K7clEacjkljj,userid1,ui001,group1
```

<details><summary>show</summary>
<p>

```bash
Edit /etc/kubernetes/manifests/kube-apiserver.yaml
Add option
--token-auth-file=/etc/kubernetes/token.csv

Add volume to mount token file
  
Add volumeMount for token file
```
  
Wait for restart of kube-apiserver
```
curl -v -k https://controlplane:6443/api/v1/pods \
  --header "Authorization: Bearer 8j79724ja8K7clEacjkljj"
```

https://kubernetes.io/docs/reference/access-authn-authz/authentication/#static-token-file
  
</p>
</details>

#### Create a Service Account called svc-myapplication and show the token. Use the token to make a call to the api server.

<details><summary>show</summary>
<p>

```
k create serviceaccount svc-myapplication
```
  
```
k get sa
  
NAME                SECRETS   AGE
default             1         7m37s
svc-myapplication   1         3s
```
  
```
k describe sa/svc-myapplication 
  
Name:                svc-myapplication
Namespace:           default
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   svc-myapplication-token-bbght
Tokens:              svc-myapplication-token-bbght
Events:              <none>  
```

```
k get secret/svc-myapplication-token-bbght
NAME                            TYPE                                  DATA   AGE
svc-myapplication-token-bbght   kubernetes.io/service-account-token   3      4m35s
```
 
  
```
k describe secret/svc-myapplication-token-bbght
Name:         svc-myapplication-token-bbght
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: svc-myapplication
              kubernetes.io/service-account.uid: ad78f9c8-c3b9-4ef4-a4f1-93955ca4a351

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     570 bytes
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Ii01amZFWlZFdVNLSWdKaVBYLUFPZlBSUTFlTjA0OXU3UDEzOGd0eG8zWnMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InN2Yy1teWFwcGxpY2F0aW9uLXRva2VuLWJiZ2h0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6InN2Yy1teWFwcGxpY2F0aW9uIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiYWQ3OGY5YzgtYzNiOS00ZWY0LWE0ZjEtOTM5NTVjYTRhMzUxIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OmRlZmF1bHQ6c3ZjLW15YXBwbGljYXRpb24ifQ.IQZU5X8Ka-XIEWbeRp0KjxgMpP9Mkpcf8FAmGCygOa6GAIkMeXJMUK-ow-iVR2QWYGAwzzspdpVqVhI7o8AVhyJNmxZWIOCT18yLIAwNq6tZuvaQL9Vas3X_ZatrSif_XLOvnPE4ASdpoTIHO-mcbcXs_zpjjgD7hAAGIcWVz4di0rMarxR_V6DXDrkh4acZJM7u64udwDaMLwQ_NrASCRTn83PTFl-0OnYhCBoZaVDOCLKP12OkpjXkRjd2HCj2CbRR2cqiWrnjPw91Qm4Xihide7jIWCJQdyInbEaHiHf8cOqBkWHUoie4rGw3vdQlYeU7bNSJpIIdhccxNSUp6A
  
```

```  
curl https://controlplane:6443/api --insecure \
  --header "Autorization: Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6Ii01amZFWlZFdVNLSWdKaVBYLUFPZlBSUTFlTjA0OXU3UDEzOGd0eG8zWnMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InN2Yy1teWFwcGxpY2F0aW9uLXRva2VuLWJiZ2h0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6InN2Yy1teWFwcGxpY2F0aW9uIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiYWQ3OGY5YzgtYzNiOS00ZWY0LWE0ZjEtOTM5NTVjYTRhMzUxIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OmRlZmF1bHQ6c3ZjLW15YXBwbGljYXRpb24ifQ.IQZU5X8Ka-XIEWbeRp0KjxgMpP9Mkpcf8FAmGCygOa6GAIkMeXJMUK-ow-iVR2QWYGAwzzspdpVqVhI7o8AVhyJNmxZWIOCT18yLIAwNq6tZuvaQL9Vas3X_ZatrSif_XLOvnPE4ASdpoTIHO-mcbcXs_zpjjgD7hAAGIcWVz4di0rMarxR_V6DXDrkh4acZJM7u64udwDaMLwQ_NrASCRTn83PTFl-0OnYhCBoZaVDOCLKP12OkpjXkRjd2HCj2CbRR2cqiWrnjPw91Qm4Xihide7jIWCJQdyInbEaHiHf8cOqBkWHUoie4rGw3vdQlYeU7bNSJpIIdhccxNSUp6A"
```

</p>
</details>

#### Mount the previously created service account into a pod.
<details><summary>show</summary>
<p>

```
pod.spec.serviceAccountName
  
kubectl describe pod/<name>
```

</p>
</details>

#### Create a new user key and certificate.

<details><summary>show</summary>
<p>
```bash
openssl genrsa -out dave.key 2048
openssl req -new -key dave.key -out dave.csr
```

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>

#### Submit the user's certificate signing request.

<details><summary>show</summary>
<p>

```bash
cat dave.csr | base64 | tr -d "\n"
```  

Use the output in the csr
  
```bash
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dave
spec:
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZVzVuWld4aE1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTByczhJTHRHdTYxakx2dHhWTTJSVlRWMDNHWlJTWWw0dWluVWo4RElaWjBOCnR2MUZtRVFSd3VoaUZsOFEzcWl0Qm0wMUFSMkNJVXBGd2ZzSjZ4MXF3ckJzVkhZbGlBNVhwRVpZM3ExcGswSDQKM3Z3aGJlK1o2MVNrVHF5SVBYUUwrTWM5T1Nsbm0xb0R2N0NtSkZNMUlMRVI3QTVGZnZKOEdFRjJ6dHBoaUlFMwpub1dtdHNZb3JuT2wzc2lHQ2ZGZzR4Zmd4eW8ybmlneFNVekl1bXNnVm9PM2ttT0x1RVF6cXpkakJ3TFJXbWlECklmMXBMWnoyalVnald4UkhCM1gyWnVVV1d1T09PZnpXM01LaE8ybHEvZi9DdS8wYk83c0x0MCt3U2ZMSU91TFcKcW90blZtRmxMMytqTy82WDNDKzBERHk5aUtwbXJjVDBnWGZLemE1dHJRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBR05WdmVIOGR4ZzNvK21VeVRkbmFjVmQ1N24zSkExdnZEU1JWREkyQTZ1eXN3ZFp1L1BVCkkwZXpZWFV0RVNnSk1IRmQycVVNMjNuNVJsSXJ3R0xuUXFISUh5VStWWHhsdnZsRnpNOVpEWllSTmU3QlJvYXgKQVlEdUI5STZXT3FYbkFvczFqRmxNUG5NbFpqdU5kSGxpT1BjTU1oNndLaTZzZFhpVStHYTJ2RUVLY01jSVUyRgpvU2djUWdMYTk0aEpacGk3ZnNMdm1OQUxoT045UHdNMGM1dVJVejV4T0dGMUtCbWRSeEgvbUNOS2JKYjFRQm1HCkkwYitEUEdaTktXTU0xMzhIQXdoV0tkNjVoVHdYOWl4V3ZHMkh4TG1WQzg0L1BHT0tWQW9FNkpsYWFHdTlQVmkKdjlOSjVaZlZrcXdCd0hKbzZXdk9xVlA3SVFjZmg3d0drWm89Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
EOF
```
  
https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>

#### Approve the previous certificate signing request.

<details><summary>show</summary>
<p>

```bash
k get csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
dave        10m   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Pending
csr-ht6ws   28m   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
  
k certificate approve dave
certificatesigningrequest.certificates.k8s.io/dave approved
  
```

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>


#### Retrieve the certificate and save in the path specified below

Save the certificates in the following locations

  /etc/kubernetes/pki/users/dave.crt
  
  /etc/kubernetes/pki/users/dave.key    
  
<details><summary>show</summary>
<p>

```bash
kubectl get csr/dave -o yaml  
Retrieve spec.status.certificate  
```

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>
    
#### Access the API as dave using the curl command. Do not update your config yet.
  
<details><summary>show</summary>
<p>

```bash
curl https://controlplane:6443/api/v1/pods \
  --key /etc/kubernetes/pki/users/dave.key  \
  --cert /etc/kubernetes/pki/users/dave.crt \
  --cacert /etc/kubernetes/pki/ca.crt
  
```

</p>
</details>  

#### Update the certificate in your config.yaml adding user dave

<details><summary>show</summary>
<p>

Backup your ~/.kube/config.yaml
  
Add dave to your config  
```bash
k config view
vi ~/.kube/config
  
users:
- name: dave
  user:
    client-certificate: /etc/kubernetes/pki/users/dave.crt
    client-key: /etc/kubernetes/pki/users/dave.key    
  
```

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>  
  
#### Where can you find the ca servers root certificate and private key?

<details><summary>show</summary>
<p>

```bash
/etc/kubernetes/manifests/kube-controller-manager.yaml
  --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
  --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
```

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>

#### Access the apis url using kube-proxy.

<details><summary>show</summary>
<p>

Config must be setup on another server other than the controlplane.  
  
```bash
In one session, run
  kubectl proxy

In another session browse http://localhost:8001/v1/apis or use curl -k http://localhost:8001/v1/apis
```

https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
  
</p>
</details>
  
https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/

https://kubernetes.io/docs/reference/access-authn-authz/kubelet-authn-authz/  
  
https://kubernetes.io/docs/reference/config-api/kubelet-config.v1beta1/  

#### Where do you find the kubelet config file?
<details><summary>show</summary>
<p>

```bash
kubelet.service
  --config=/var/lib/kubelet/kubelet-config.yaml
```
or
```bash
ps -ef | grep kubelet
  --config=/var/lib/kubelet/kubelet-config.yaml
```
</p>
</details>

#### What kubelet port allows for unauthenticated read-only access? 
<details><summary>show</summary>
<p>

This port and access may have been deprecated or removed in newer kubernetes versions.
```bash
10255
```
</p>
</details>

#### What kubelet ports allows full access?
<details><summary>show</summary>
<p>

```bash
10250
```

</p>
</details>

#### How do you access syslog through the kubelet api?
<details><summary>show</summary>
<p>

```bash
curl -ks https://localhost:10250/logs/syslog
```

</p>
</details>

#### Access the kubelet metris api.
<details><summary>show</summary>
<p>

```bash
curl -ks https://localhost:10255/metrics
```

Port must be non-zero  
```bash  
--read-only-port=10255
```

```yaml
apiVersion: kubelet.config.k8s.io/v1beta
kind: KubeletConfiguration
readOnlyPort: 0
```
  
</p>
</details>

#### How do you disable anonymous access in the kubelet service?
<details><summary>show</summary>
<p>

```bash
kubelet.service
  --anonymous-auth=false
```
or
```yaml
apiVersion: kubelet.config.k8s.io/v1beta
kind: KubeletConfiguration
authentication:
  anonymous:
    enabled: false
```
  
</p>
</details>

#### How do you authenticate against the kubernetes api with the key,cert?
<details><summary>show</summary>
<p>

```bash
curl -k https://localhost/10250/    \
  --key  \
  --cert
  --
```

</p>
</details>  

#### Setup kubectl proxy to connect to the api. Return the version of kubernetes. Return the list of pods.
<details><summary>show</summary>
<p>

```bash
kubectl proxy
```
  
```bash
curl -k http://localhost:8001/version
{
  "major": "1",
  "minor": "23",
  "gitVersion": "v1.23.0",
  "gitCommit": "ab69524f795c42094a6630298ff53f3c3ebab7f4",
  "gitTreeState": "clean",
  "buildDate": "2021-12-07T18:09:57Z",
  "goVersion": "go1.17.3",
  "compiler": "gc",
  "platform": "linux/amd64"
}
```

```bash
curl -k http://localhost:8001/api/v1/pods  
```
  
</p>
</details>  
  
#### Create the following pod and service. Configure port-forward'ing to the service. 
```yaml

```
  
<details><summary>show</summary>
<p>

```bash

```

</p>
</details>  
  
## Use Role Based Access Controls to minimize exposure
#### Identify 6 Authorization Modes and what field in the api-server can they be set.
<details><summary>show</summary>
<p>

```
AlwaysAllow
NODE
ABAC
RBAC
Webhook
AlwaysDeny
  
--authorization-mode=NODE,RBAC,Webhook  
```

https://kubernetes.io/docs/reference/access-authn-authz/authorization/
  
</p>
</details>

#### List the actions a role can perform.

<details><summary>show</summary>
<p>

```bash
"get", "list", "watch", "create", "update", "patch", "delete"
```

</p>
</details>
  
#### Create a namespace dev. Apply the following to this namespace. Create a developer role and a developer-binding rolebinding, giving dave access. Role must be able to list and get resources of type pods. Always check whether the user has access first before adding.

<details><summary>show</summary>
<p>

```bash
k auth can-i list pods --as dave -n dev
k create role developer --verb=list,get --resource=pods -n dev
k create rolebinding developer-binding --role=developer --user=dave -n dev
k auth can-i list pods --as dave -n dev
```

</p>
</details>

#### Dave is now a storage admin. Create a storage-admin clusterrole and a storage-admin clusterrolebinding adding dave to the clusterrolebinding.

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>
  
#### Clusterbinding with role access. TBD

<details><summary>show</summary>
<p>

```bash
```

</p>
</details>
  
## Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones.
### Questions
#### Question

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

## Update Kubernetes frequently
### Questions
  

  
#### 
<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

  #### 
<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

#### 
<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

#### 
<details><summary>show</summary>
<p>

```bash

```

</p>
</details>
