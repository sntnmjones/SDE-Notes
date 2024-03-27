### Get information about availability zones

```

aws ec2 describe-availability-zones --region $region | jq '.'

```

  
  

### Get the AZ of the subnets in use

```

aws ec2 describe-subnets --output json --region $region |

jq '.Subnets[]' |

jq '.AvailabilityZone' |

sort |

uniq

```

or

```

isengard -r $region aws scroll-dev+prod-$airport_code-cell_0001@amazon.com ReadOnly ec2 describe-subnets --region $region --filter "Name=tag:aws:cloudformation:logical-id,Values=*PrivateSubnet0*" --query "Subnets[*].[Tags[?Key == 'aws:cloudformation:logical-id'].Value, AvailabilityZone] | []"

```

  

### Get ip of specific instance

```

aws ec2 describe-instances --region us-east-1 --instance-ids i-00018e34a583617f2 | jq '.Reservations[].Instances[].PrivateDnsName'

```

  

### Get an internal ip of ec2 instance

```

isengard -r $region aws scroll-dev+prod-$airport_code-cell_0001@amazon.com ReadOnly ec2 describe-instances | jq -r '[.Reservations[].Instances[] | select(.Tags[]?.Value|contains("Query"))][0] | .PrivateDnsName'

```

or

```

isengard -r $region aws scroll-dev+prod-$airport_code-cell_0001@amazon.com ReadOnly ec2 describe-instances --filters 'Name=tag-value,Values=Query' --query 'Reservations[0].Instances[0].PrivateDnsName' | cut -d'"' -f 2

```

  

### Find number of instances in an ASG of an account

```

isengard -r eu-west-2 aws 668675357212 ReadOnly autoscaling describe-auto-scaling-groups --query 'AutoScalingGroups[*].[AutoScalingGroupName,DesiredCapacity]' | grep -A 1 StorageWorker

```