# Cluster Hardening (15%)

* Restrict access to Kubernetes API

* Use Role Based Access Controls to minimize exposure

* Exercise caution in useing service accounts e.g. disable defaults, minimize permissions on newly created once.

* Update Kubernetes frequently

## References
github.com > cncf> curriculum> [CKS Curriculum](https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.23.pdf)

kubernetes.io > Documentation > Reference > kubectl CLI > [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

https://kubernetes.io/docs/reference/access-authn-authz/authentication

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

#### Add a new user to the cluster and approve by generating the keys and a certificate signing request.

<details><summary>show</summary>
<p>

```bash

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
  
  
## Update Kubernetes frequently
### Questions
#### Rotate the cluster certificates

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

  #### Question

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>


## Use Role Based Access Controls to minimize exposure
### References
#### Question
<details><summary>show</summary>
<p>

```

```

</p>
</details>

## Exercise caution in useing service accounts e.g. disable defaults, minimize permissions on newly created once.
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
#### Question

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

