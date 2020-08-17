## Cheatsheet
##### Current status

`$ getenforce`

##### Change SELinux mode

`$ setenforce 0` &rarr; set to permissive mode
`$ setenfoce Permissive` &rarr; set to permissive mode
`$ setenforce 1` &rarr; set to enforcing mode
`$ setenfoce Enforcing` &rarr; set to enforcing mode

System should be rebooted after these settings.

##### List SELinux contexts

Only add `-Z`to default display commands. Examples;

`$ ls -alZ`
`$ ps -efZ`
`$ ìd -Z`
`...`

##### Booleans

* List booleans

  `semanage boolean -l`
* 
