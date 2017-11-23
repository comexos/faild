# faild – Poor man's failover daemon

## What is faild?

faild monitors nodes on the same network for availability and in case of errors tries to claim their ip addresses.
This can be useful when there is no load balancer available or the setup of some cluster resource manager like pacemaker would be an overkill.
The daemon is written in bash, for monitoring it uses arping provided by iputils, for assigning the ip addresses ip provided by iproute2.

## Getting started

### Requirements

* bash >= 4
* arping (provided by iputils)
* ip

### Installation

copy faild to /usr/sbin/ and make it executable
```bash
sudo cp faild /usr/sbin; chmod a+x /usr/sbin/faild
```

copy faild.conf to /etc/
```bash
sudo cp faild.conf /etc
```

edit /etc/faild.conf
```bash
# HOSTS=(
#     '<IP> <NETMASK> <MAC> <DEVICE> <ALIAS>'
#     '1.2.3.4 24 00:00:00:00:00:00 eth0 1'
# )
HOSTS=(
    '10.10.10.1 24 01:23:45:67:89:AB eth0 1'
    '10.10.10.2 24 01:23:45:67:89:CD eth0 2'
)

# timeout in seconds after which a host is declared dead
PING_TIMEOUT=2

# how often should a host be checked before being declared dead
PING_COUNT=2

# how many seconds to wait between checks
CHECK_INTERVAL=0
```

If you use systemd, just copy faild.service to /lib/systemd/system/ and activate it
```bash
sudo cp faild.service /lib/systemd/system
sudo systemctl enable faild.service
sudo systemctl daemon-reload
sudo systemctl restart faild.service

```

If you use something else, make sure you launch faild on startup and log stderr to some log file.

### Troubleshooting

Checking the logs
```bash
journalctl -u faild.service
```

Reload the configuration using systemd
```bash
systemctl reload faild.service
```

Reload the configuration manually
```bash
kill -1 `cat /var/run/faild.pid`
```

Log current status
```bash
kill -USR1 `cat /var/run/faild.pid`
```

## Authors
* **Thomas Kaltenbrunner** - *initial work* - [comexos](https://github.com/comexos)

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
