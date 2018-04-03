

this is what i did:

added the following in cluster.yml
```
---
- hosts: localhost
  gather_facts: False
+  tasks:
+    - name: debug
+      debug:
+        msg: "{{ some_test_var }}"
  roles:
    - { role: kubespray-defaults}
    - { role: bastion-ssh-config, tags: ["localhost", "bastion"]}

- hosts: k8s-cluster:etcd:calico-rr
  any_errors_fatal: "{{ any_errors_fatal | default(true) }}"
  gather_facts: false
  vars:
    # Need to disable pipelining for bootstrap-os as some systems have requiretty in sudoers set, which makes pipelining
    # fail. bootstrap-os fixes this on these systems, so in later plays it can be enabled.
    ansible_ssh_pipelining: false
  roles:
    - { role: kubespray-defaults}
    - { role: bootstrap-os, tags: bootstrap-os}
```

put some_test_var at the top of k8s-cluster.yml file

```
#test 
some_test_var: "testing!"
```

ran ansible
```
ansible-playbook -u root -b -v  -i inventory/inventory.cfg cluster.yml
```

this is the output
```
TASK [debug] **********************************************************************************
Tuesday 03 April 2018  23:23:04 +0530 (0:00:00.031)       0:00:00.872 ********* 
fatal: [127.0.0.1]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'some_test_var' is undefined\n\nThe error appears to have been in '/Users/shashwat/github/kubespray-2.4.0/cluster.yml': line 5, column 7, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n  tasks:\n    - name: debug\n      ^ here\n"}
```
