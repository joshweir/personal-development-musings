# Redis Notes

Determine the master node and connect to it:

```bash
redis-cli -h 127.0.0.1 -p 6379 info | grep master
#note values in master_host, master_port and connect:
redis-cli -h [master_host] -p [master_port]
```

Connect to redis on `192.168.1.139:6379`, scan 2000 entries, match keys: "cache:views/sidebar*":

```bash
redis-cli -p 6379 -h 192.168.1.139 SCAN 0 MATCH "cache:views/sidebar*" count 2000
```

and delete them:

```bash
redis-cli -p 6379 -h 192.168.1.139 SCAN 0 MATCH "cache:views/sidebar*" count 2000 | xargs redis-cli -p 6379 -h 192.168.1.139 del
```
