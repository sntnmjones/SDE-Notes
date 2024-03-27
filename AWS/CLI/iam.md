### Get role

```

accountId=838986795827 && \

region=us-east-1 && \

isengard -r $region aws $accountId ReadOnly iam get-role --role-name ScrollOpsRole

```

  

### Get role policy

```

accountId=838986795827 && \

region=us-east-1 && \

isengard -r $region aws $accountId ReadOnly iam get-role-policy

```

  

### Print all policies for a user

```

ada credentials update --account 426169273327 --provider conduit --role IibsAdminAccess-DO-NOT-DELETE --once && \

username=cloudWatch && \

aws iam list-user-policies --user-name $username | jq -r '.PolicyNames[]' | xargs -I {} aws iam get-user-policy --user-name $username --policy-name {} && \

aws iam list-attached-user-policies --user-name $username | jq -r '.AttachedPolicies[].PolicyName'

```