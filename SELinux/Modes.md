#### There are three types of modes

* **Enforcing**,Rules are enforced and violations are logged.
* **Permissive**,Only violations logged
* **Disabled**, Nothing is done by about SELinux

#### Displaying or setting mode

* `getenforce`,`sestatus` or /etc/sysconfig/selinux  -> get current mode
* `setenforce permissive` or editing /etc/sysconfig/selinux  -> set mode to permissive
* to run `setenforce`, SELinux must be enabled. To enable it should be configured in /etc/sysconfig/selinux and rebooted.
* to take into a selinux disabled system to `enforcing` mode, it should be taken to permissive mode firstly and rebooted. Then enforcing mode and reboot.
