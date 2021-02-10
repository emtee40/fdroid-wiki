

There are a number of _buildserver_ instances which community members can get
access to.  You do not need to be a core contributor to get access.


## fdroid-buildserver-gcc136-public

```config
Host gcc136.osuosl.org
  Port 45015
  User tunnel

Host fdroid-buildserver-gcc136-public
  User fdroid
  PasswordAuthentication no 
  ProxyCommand ssh -qax -W 192.168.78.135:22 tunnel@gcc136.osuosl.org
  ServerAliveInterval 30
  ServerAliveCountMax 5
```

