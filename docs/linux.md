## Monitoring temperature on Linux

[Monitoring CPU and GPU Temperatures on Linux](https://itsfoss.com/monitor-cpu-gpu-temp-linux/)

From [a Unix & Linux Stack Exchange question](https://unix.stackexchange.com/questions/367302/best-good-tool-for-monitoring-a-server-with-web-interface):

- Nagios: seems to be the best choice (standard)
- [Munin](http://munin-monitoring.org/)
  - [Installation (Digital Ocean)](https://www.digitalocean.com/community/tutorials/how-to-install-the-munin-monitoring-tool-on-debian-8)
- [Cacti](https://cacti.net/)
- [Monitorix](https://www.monitorix.org/doc-debian.html)

## Solve (eternal) locales problems

To solve locales problems (LC_ALL, LC_CTYPE, ...), simply comment the following line in `/etc/ssh/sshd_config`:
```
AcceptEnv LC_*
```

## SSH Agent Forwarding

### From a user to root (doing sudo)

The goal is to tell `sudo` to transfer the environment variable `SSH_AUTH_SOCK` to root.
For that, open the /etc/sudoers file

[[StackOverflow source]](https://serverfault.com/questions/107187/ssh-agent-forwarding-and-sudo-to-another-user)