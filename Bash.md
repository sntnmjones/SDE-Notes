## Commands
### date
#### Print date in ISO-8601
```bash
date +"%Y-%m-%dT%H:%M:%S.%3N%z
```

### hexdump
#### Read crt or hex to ascii
```bash
hexdump -C <file>.crt
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