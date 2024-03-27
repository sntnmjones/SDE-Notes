### Get item from table with streamId of 0

```

i=0

aws dynamodb get-item --region $region --table-name LogStreamAllocationTable20180721-PW4PTCCOV62L --key "{\"streamId\": {\"N\": \"$i\"}}"

```

  

### Describe all tables in a region (`-r` strips double quotes)

```

for table in `aws dynamodb list-tables --region eu-west-2 | jq -r '.TableNames[]'`;

do;

aws dynamodb describe-table --table-name $table --region eu-west-2;

done;

```

  

### Print list of tables not in on-demand

```

for table in `aws dynamodb list-tables --region eu-west-2 | jq -r '.TableNames[]'`;

do;

aws dynamodb describe-table --table-name $table --region eu-west-2 | jq -r '.Table | select(.TableStatus|test("ACTIVE")) | select(.BillingModeSummary|not) | .TableName';

done;

```

  

### Delete all tables

```

for table in $(aws dynamodb list-tables --region us-west-2 --profile jnesztr | jq -r '.TableNames[]'); do

aws dynamodb delete-table --table-name $table --profile jnesztr;

done;

```

  

### Query table

```

ada credentials update --account 426169273327 --provider conduit --role IibsAdminAccess-DO-NOT-DELETE --once 1&> /dev/null && \

workflowId=129932288 && \

aws dynamodb query --table-name applicant-workflow-store-prod --key-condition-expression "icims_id =:icims_id" --expression-attribute-values "{\":icims_id\":{\"N\":\"${workflowId}\"}}"

```