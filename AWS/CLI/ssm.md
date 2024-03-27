### Run remote command

```

region=eu-north-1 && \

accountId=638410112697 && \

instanceId=i-00849676227da604f && \

rmCmd='"#!/bin/bash","ls"' && \

command="{\"commands\":[${rmCmd}]}" && \

isengard -r $region aws $accountId ScrollOpsRole ssm send-command --document-name "AWS-RunShellScript" --targets "[{\"Key\":\"InstanceIds\",\"Values\":[\"${instanceId}\"]}]" --parameters $command

```