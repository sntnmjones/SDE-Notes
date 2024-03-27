### Get stacks where the name includes "AZ3"

```

aws cloudformation list-stacks --region $region |

jq '.StackSummaries[]? |

select(.StackName|test("AZ3"))'

```

  

### Get stacks where name includes "AZ3" and is not deleted

```

aws cloudformation --region us-east-1 list-stacks |

jq '.StackSummaries[] |

select((.StackName|test("AZ3")) and (.StackStatus|contains("DELETE_COMPLETE")|not))

```

  

### List stack resources

```

aws cloudformation --region us-east-1 list-stack-resources --stack-name Shared-SharedStack-PE4F6PI3SOAO | jq '.StackResourceSummaries[]'

```

  

### Is more than one AZ allowed

```

isengard -r ap-southeast-3 aws 233866917306 ScrollOpsRole cloudformation describe-stacks --stack-name Fleet-Egress-AZ1-1 | jq '.Stacks[].Parameters[]|select(.ParameterKey|contains("RIPMoreThanOneAz"))'

{

"ParameterValue": "false",

"ParameterKey": "RIPMoreThanOneAz"

}

```

  

### Update stack template **CAUTION: This template will delete the stacks!**

```

aws cloudformation update-stack --region us-east-1 --stack-name Fleet-StorageWorker-AZ3-1 --template-body "{\"Description\": \"Created with LPT\",\"Resources\": {\"LptThrowawayWaitConditionHandle\": {\"Type\": \"AWS::CloudFormation::WaitConditionHandle\",\"Properties\": {}}},\"Outputs\": {\"StackArn\": {\"Value\": {\"Ref\": \"AWS::StackId\"},\"Description\": \"Use this as the stack_arn in your cloud_formation_deployment_stack override.\"}}}"

```