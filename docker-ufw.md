# **Docker UFW**

This is a workaround for the Docker ufw issue. As default, Docker bypass the ufw package to manage firewall rules. This is not a good idea for production environments.

## **How to**
1. show external facing interface
```
ip addr show
```
2. Append the following at the end of `/etc/ufw/after.rules` (replace `eth0` with your external facing interface)
```
# Put Docker behind UFW
*filter
:DOCKER-USER - [0:0]
:ufw-user-input - [0:0]

-A DOCKER-USER -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A DOCKER-USER -m conntrack --ctstate INVALID -j DROP
-A DOCKER-USER -i eth0 -j ufw-user-input
-A DOCKER-USER -i eth0 -j DROP
COMMIT
```
3. Restart ```ufw```. There may be some unknown reasons cause the UFW rules will not take effect after restart UFW, please reboot servers.
```
sudo ufw reload
```

4. Check the ufw rules and make sure the rules are applied correctly.

## **References**
1. [Github Issue](https://github.com/moby/moby/issues/4737#issuecomment-419705925)
2. [Stack Overflow](https://stackoverflow.com/questions/30383845/what-is-the-best-practice-of-docker-ufw-under-ubuntu)