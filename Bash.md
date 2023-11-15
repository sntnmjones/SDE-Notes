## hexdump
### Read crt or hex to ascii
```bash
hexdump -C <file>.crt
```

## ssh
### Tunneling
#### Access local file from remote 
-L : Port forward
```
ssh -i ~/.ssh/<privateSshKey> -N -L <localPort>:<destHost>:<remotePort> <jumpHost>
```
## xxd
### Echo hex file to std out
```bash
xxd -p <file>.crt
```
