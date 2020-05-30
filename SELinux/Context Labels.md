#### SELinux utilities

sudo yum install policycoreutils policycoreutils-python setools setools-console setroubleshoot selinux-policy-devel


#### Every objects has 3 context labels

* user
* role
* type

#### Many display commands support -Z flag to show contexts

* `ls -Z`,`ps -efZ`...

#### Context types are used to create policies about which source objects have access to which target objects

For example;\

`$ ps -efZ | grep ssh` \
`system_u:system_r:sshd_t:s0-s0:c0.c1023 root 945   1  0 23:29 ?        00:00:00 /usr/sbin/sshd -D`


