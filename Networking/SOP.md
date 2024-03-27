# Change static ip

Get the primary network interface

```
$ ip addr
```

Change the ip.
In the example we are changing the address on interface ens7f0 to 10.129.3.228/25

```
$ sudo nmcli connection modify ens7f0 ipv4.address 10.129.3.228/25
```

Activate the new configuration

```
$ sudo nmcli connection up ens7f0
```

Profit!!!
