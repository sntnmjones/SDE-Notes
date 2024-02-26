### Print objects on new lines with -r
```
aws cloudwatch describe-alarms | jq -r '.MetricAlarms[] | "AlarmName: " + .AlarmName + "\nAlarmDescription: " + .AlarmDescription + "\n"'
```

```
AlarmName: Lambda-StepFuncInvokeLambda-Errors
AlarmDescription: Alarm when Lambda function errors count is greater than 1 sample
```


```
ada credentials update --account 804611410464 --provider conduit --role IibsAdminAccess-DO-NOT-DELETE --once && \
for alarm in $(aws cloudwatch describe-alarms --children-of-alarm-name CXT.ApplicantService.ApiP90LatencyAboveThreshold | jq -r '.MetricAlarms[].AlarmName'); do
  aws cloudwatch describe-alarms --alarm-names $alarm | jq -r '.MetricAlarms[] | "AlarmName: \(.AlarmName)", "> \(.Threshold) for \(.EvaluationPeriods) EvaluationPeriods for \(.DatapointsToAlarm) DatapointsToAlarm"'
done;
```

### Remove key and its attributes
`.person` being the key of interest

```
pbpaste | jq 'del(.person)'
```

### Create json 
```
$ jq -n '.foo = "bar" | .number = 42'

{
  "foo": "bar",
  "number": 42
}
```

### Create json using variables
```
jq -n '{region:$region, stage:$stage}' --arg region $REGION --arg stage $STAGE_INPUT
```