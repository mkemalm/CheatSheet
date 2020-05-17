### Port forwarding

* For the same server 

  `$ sudo firewall-cmd --zone="public" --add-forward-port=port=80:proto=tcp:toport=12345`

* For the remote server

    * Activate masquerade in the desired zone.
    
      `$ sudo firewall-cmd --zone=public --add-masquerade`

    * Add the forward rule. This example forwards traffic from local port 80 to port 8080 on a remote server located at the IP address: 198.51.100.0 

    `$ sudo firewall-cmd --zone="public" --add-forward-port=port=80:proto=tcp:toport=8080:toaddr=198.51.100.0`

* To reload

  `$ firewall-cmd --reload`

* To remove port forwarding 

  `$ sudo firewall-cmd --remove-forward-port=port=80:proto=tcp:toport=8080:toaddr=198.51.100.0`
