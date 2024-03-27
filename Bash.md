## Commands
### chown
Change the owner of a directory and its children
```sh
sudo chown -R amznadmin:amznadmin /home/amznadmin
```
### date
#### Print date in ISO-8601
```bash
date +"%Y-%m-%dT%H:%M:%S.%3N%z
```

### find
Do an operation for every file. The escaped semicolon is necessary to avoid '
find: missing argument to -exec' error

```bash
find configuration/mtls -type f -exec chmod 644 {} \;
```

### hexdump
#### Read crt or hex to ascii
```bash
hexdump -C <file>.crt
```

### ldd
list dynamic dependencies
-v for more verbose output
https://www.howtoforge.com/linux-ldd-command/
```
ldd sar_generate_certificate_request
        linux-vdso.so.1 (0x00007fffbc50d000)
        libjson-c.so.3 => /lib64/libjson-c.so.3 (0x00007f5f31223000)
        libdl.so.2 => /lib64/libdl.so.2 (0x00007f5f3121e000)
        libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007f5f30e00000)
        libm.so.6 => /lib64/libm.so.6 (0x00007f5f31143000)
        libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007f5f31128000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f5f31121000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f5f30a00000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f5f3123d000)
```

### rsync
#### Copy folder from one host to another

Goes from source to destination  
  
`rsync -avh --progress .aws [dev-dsk-jnesztr-2b-4f539faa.us-west-2.amazon.com](http://dev-dsk-jnesztr-2b-4f539faa.us-west-2.amazon.com/):`  
  
or `rsync --recursive jnesztr.aka.corp.amazon.com:MacWorkplace/CandidatePortalTest/build/CandidatePortalTest/CandidatePortalTest-1.0/AL2_x86_64/DEV.STD.PTHREAD/build/brazil-integ-tests/ss ~/Downloads/Tmp`

### scp
From local to server
```bash
scp -i ~/.ssh/id_rsa utimaco/Administration/p11tool2 ssm-user@i-0e005241698fa326c:/home/ssm-user
```

From server to local
```bash

```
### set
The line `set -o nounset -o pipefail -o errexit` sets three options that affect how your Bash scripts behave:

- **`nounset` (or `-u`)**: This option makes accessing an **unset variable** an error. Any attempt to use a variable that hasn't been explicitly assigned a value will cause the script to exit with an error code. This helps prevent bugs caused by typos or relying on unintended variable values.
- **`pipefail`**: This option affects the exit code of commands involving pipes (`|`). It ensures that the exit code of the entire pipe reflects the status of the **first failing command**, not just the last executed one. This helps avoid misleading situations where a successful command masks a previous error.
- **`errexit` (or `-e`)**: This option makes your script exit upon encountering any **non-zero exit code**. Any command that fails for any reason will terminate the entire script. This helps catch errors early and prevents further execution that may depend on successful previous steps.

**In summary, this line makes your scripts stricter and more explicit in their behavior by:**

- Preventing accidental reliance on unset variables.
- Accurately reflecting errors in piped commands.
- Failing early upon encountering any command failures.
### shasum
Hash files
```bash
shasum -a 256 *
```
### ssh
#### Tunneling
##### Access local file from remote 
-L : Port forward
```
ssh -i ~/.ssh/<privateSshKey> -N -L <localPort>:<destHost>:<remotePort> <jumpHost>
```

#### Errors
##### kex_exchange_identification: Connection closed by remote host
This error can happen when the host is offline (e.g. reboot)


### tcpdump
https://www.cyberciti.biz/howto/question/man/tcpdump-man-page-with-examples.php
Listen on a port
```
sudo tcpdump port 443 and '(tcp-syn|tcp-ack)!=0' | grep 50.231.162.106
```

### xxd
#### Echo hex file to std out
```bash
xxd -p <file>.crt
```

## Mechanisms
### Logging
Result : 2024-02-14T17:54:34.487+0000] message
```sh
# Create log file with epoch  
mkdir -p /home/ssm-user/var/output/logs  
logdir="/home/ssm-user/var/output/logs"  
logfile="$logdir/application_$(date +%s).log"  
touch $logfile  
  
# Append all output to log file  
exec &> $logfile  
  
# Log function for correlating timestamps  
function log() {  
  # Log stdout and errors to file  
  printf "[$(date +"%Y-%m-%dT%H:%M:%S.%3N%z")] $1\n" >> $logfile 2>&1  
}
```

### Retry script on error
```sh
# Exit on error, throwing ERR trace for trap  
set -eE  
  
# Error handling  
retry_counter="/home/ssm-user/.retry_counter"  
trap cleanup ERR  
  
cleanup() {  
  # Script runs on reboot. Try rerunning script 3 times. Then reset retry counter by removing file.  
  if [ -f "$retry_counter" ]; then  
    count=$(cat "$retry_counter")  
    if [ "$count" -lt 3 ]; then  
      count=$(( count + 1 ))  
      echo $count > "$retry_counter"  
      log "Script failed. Rebooting..."  
      reboot  
    else  
      log "Did not launch script after $count retries"
	  rm "$retry_counter"  
      exit  
    fi  
  else    
    echo 1 > "$retry_counter"  
  fi  
}
```