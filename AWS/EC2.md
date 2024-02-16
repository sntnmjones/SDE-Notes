## User Data
This is used to configure hosts during creation.


Location on host: `/var/lib/cloud/instances/i-08a36f8e27ebb6b70/user-data.txt`


Run user data on reboot:  
```sh
# Inside user data script
# Create cloud config so user data will be executed on every reboot
# https://www.akto.io/blog/how-to-run-bash-commands-on-aws-ec2-instance-restart
printf "cloud_final_modules:\n- [scripts-user, always]\n" > /etc/cloud/cloud.cfg.d/cloud-config.cfg
```

