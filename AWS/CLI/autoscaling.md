Get StorageWorker autoscaling groups

```

aws autoscaling describe-auto-scaling-groups --region us-east-1 |

jq '.AutoScalingGroups[] |

select(.AutoScalingGroupName|test("StorageWorker"))'

```

  

Get StorageWorker autoscaling groups and their availability zone

```

aws autoscaling describe-auto-scaling-groups --region us-east-1 |

jq '.AutoScalingGroups[] |

select(.AutoScalingGroupName|test("StorageWorker")) |

"\(.AutoScalingGroupName), \(.AvailabilityZones[]), \(.VPCZoneIdentifier)"' |

sort

```

  

Get instance id from asg

```

aws autoscaling describe-auto-scaling-groups --region us-east-1 --auto-scaling-group-names Query-AZ1-1 | jq '.AutoScalingGroups[].Instances[0].InstanceId'

```