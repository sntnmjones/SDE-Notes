## hexdump
### Read crt or hex to ascii
```bash
hexdump -C <file>.crt
```

## scp
From local to server
```bash
scp -i ~/.ssh/id_rsa utimaco/Administration/p11tool2 ssm-user@i-0e005241698fa326c:/home/ssm-user
```

From server to local
```bash

```
## ssh
### Tunneling
#### Access local file from remote 
-L : Port forward
```
ssh -i ~/.ssh/<privateSshKey> -N -L <localPort>:<destHost>:<remotePort> <jumpHost>
```

### Errors
#### kex_exchange_identification: Connection closed by remote host
This error can happen when the host is offline (e.g. reboot)

## xxd
### Echo hex file to std out
```bash
xxd -p <file>.crt
```
