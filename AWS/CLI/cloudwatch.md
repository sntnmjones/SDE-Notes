### Get alarms with name as argument

```

ada credentials update --account 804611410464 --provider conduit --role IibsAdminAccess-DO-NOT-DELETE --once && \

aws cloudwatch describe-alarms --alarm-types CompositeAlarm | jq -r '.CompositeAlarms[] | select(.AlarmName|test("Percent_disk_space_used")) | .AlarmName'

```

  

### Get alarms and thresholds from composite alarm

```

ada credentials update --account 804611410464 --provider conduit --role IibsAdminAccess-DO-NOT-DELETE --once && \

for alarm in $(aws cloudwatch describe-alarms --children-of-alarm-name CXT.ApplicantService.ApiP90LatencyAboveThreshold | jq -r '.MetricAlarms[].AlarmName'); do

aws cloudwatch describe-alarms --alarm-names $alarm | jq -r '.MetricAlarms[] | "AlarmName: \(.AlarmName)", "> \(.Threshold) for \(.EvaluationPeriods) EvaluationPeriods for \(.DatapointsToAlarm) DatapointsToAlarm"'

done;

```