# Cluster Setup (10%)

* Use Network security policies to restrict cluster level access

* Use CIS benchmark to review the security configuration of Kubernetes components (etcd, kubelet, kubedns, kubeapi)

* Propely setup Ingress objects with security control

* Protect node metadata and endpoints 

* Minimize use of and access to GUI elements

* Verify platform binaries before deploying

## References
github.com > cncf> curriculum> [CKS Curriculum](https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.23.pdf)
kubernetes.io > Documentation > Reference > kubectl CLI > [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

## Use Network security policies to restrict cluster level access
### References
https://kubernetes.io/docs/concepts/services-networking/network-policies/

### Questions

#### Create an ingress policy to deny all outbound traffic.

<details><summary>show</summary>
<p>

```YAML
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
spec:
  podSelector: {}
  policyTypes:
  - Egress
```


</p>
</details>

#### Create an ingress policy to deny all inbound traffic.
<details><summary>show</summary>
<p>
  
```YAML
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```
</p>
</details>

#### Create an ingress policy to all inbound traffic from namespace finance and accounting.
<details><summary>show</summary>
<p>

```YAML
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: it
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  TBD
```

</p>
</details>


## Use CIS benchmark to review the security configuration of Kubernetes components (etcd, kubelet, kubedns, kubeapi)

### References
* [CIS Assessor install](https://www.cisecurity.org/cis-benchmarks/)
* [Kube-bench install](https://github.com/aquasecurity/kube-bench/releases)

#### What tools can be used to identify best practices for your cluster. 

<details><summary>show</summary>
<p>

```
CIS Assessor
kube-bench 
```

</p>
</details>

#### Run CIS CAT-PRO with help

<details><summary>show</summary>
<p>

```bash
./Assessor-CLI.sh -h
```
</p>
</details>

#### Run CIS CAT-PRO interactively, no timestamp, index prefix

<details><summary>show</summary>
<p>

```bash
./Assessor-CLI.sh
  -i (interactive)
  -rd (dir)
  -nts (no timestamp)
  -rp index (report prefix)
```
</p>
</details>

#### Run kube-bench. Set the config directory. Set the config. 

<details><summary>show</summary>
<p>

```bash
./kube-bench --config-dir cfg --config cfg/config.yaml
```

```
...
== Summary ==
0 checks PASS
0 checks FAIL
24 checks WARN
0 checks INFO
```
</p>
</details>

#### What are the configuration items for Kubelet?
<details><summary>show</summary>
<p>

Easily generate YAML with:

```bash
kubectl run nginx --image=nginx --restart=Never --dry-run=client -n mynamespace -o yaml > pod.yaml
```

```bash
cat pod.yaml
```

```bash
kubectl create -f pod.yaml
```

sdfsdjflkjlllllll

```bash
sdfsdlkfjskljdsf
```

</p>
</details>

  

## Propely setup Ingress objects with security control

### Qustions

#### Setup TLS for an ingress

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

#### Setup Ingress Class

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>


## Protect node metadata and endpoints 

### Questions

#### Disable readonly port in Kubelet

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

## Minimize use of and access to GUI elements

### Questions

#### This is a sample question

<details><summary>show</summary>
<p>

```bash

```


</p>
</details>

## Verify platform binaries before deploying

### Questions

#### Download the kube-api binary and verify the checksum matches the website.

<details><summary>show</summary>
<p>

```bash
```

</p>
</details>
