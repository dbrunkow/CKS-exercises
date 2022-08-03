# Monitoring, Logging and Runtime Security (20%)

* Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities

* Detect threats within physicial infrastructure, apps, networks, data, users and workloads

* Detect all phases of attack regardless where it occurs and how it spreads

* Perform deep analytical investigation and identification of bad actors within environment

* Ensure immutability of containers at runtime
* Use Audit Logs to monitor access

## References
github.com > cncf> curriculum> [CKS Curriculum](https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.23.pdf)
kubernetes.io > Documentation > Reference > kubectl CLI > [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

## Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities
### References
### Questions
#### What is strace? Run it and generate the help page.
<details><summary>show</summary>
<p>

```
Tool to trace system calls and signals.  
```

</p>
</details>

#### Use strace to identify the syscalls used when touching a file; touch /tmp/cks.txt

<details><summary>show</summary>
<p>

```

```

</p>
</details>

#### What is falco? Hint, has nothing to do with Amadeus but you may sing the song if you wish.

https://falco.org/docs/

<details><summary>show</summary>
<p>

```
A security tool to signal an alert when a special condition is satisfied.
```

</p>
</details>

#### What are the 4 types of falco rules?
<details><summary>show</summary><p>

```
Descriptions, Conditions, Output, Priority.
```
</p></details>

#### Where can you find Falco to install?
<details><summary>show</summary><p>
  
```
Go to the falco.org site and select the download option.
https://falco.org/docs/getting-started/download/
```
</p></details>

#### What port does falco listen on?
<details><summary>show</summary><p>
  
```
8765
```
</p></details>

#### Where are the falco rules files?
<details><summary>show</summary><p>
  
```
/etc/falco/
```
</p></details>

#### Run falco. Go to another shell and mkdir -p /etc/falco/foo
<details><summary>show</summary><p>
  
```

```
</p></details>


#### What is sysdig?
<details><summary>show</summary><p>

```
Open source system monitoring tool.
```
</p></details>

#### Run sysdig.
<details><summary>show</summary><p>

```
# sysdig
```
</p></details>

#### Run sysdig against nano.
<details><summary>show</summary><p>

```
# sysdig proc.name=nano
```
Enter another shell and run nano. You should see the progression of syscalls.
  
</p></details>


## Detect threats within physicial infrastructure, apps, networks, data, users and workloads
### References
#### Question

<details><summary>show</summary>
<p>

```
Answer
```

</p>
</details>

## Detect all phases of attack regardless where it occurs and how it spreads
### Questions
#### Question

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

## Perform deep analytical investigation and identification of bad actors within environment
### Questions
#### Question

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

## Ensure immutability of containers at runtime
### Questions
#### Create a pod that cannot write to the filesystem.

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

#### Create a pod that cannot elevate user to root.

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>


## Use Audit Logs to monitor access
### Questions
#### Question

<details><summary>show</summary>
<p>

```bash

```

</p>
</details>

