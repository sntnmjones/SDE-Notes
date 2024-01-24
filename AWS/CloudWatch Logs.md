## Parse Insights into regex
```
fields AwsAccountId, metering.TransformedBytesOut, metering.AddRecordTimestampSeconds
| parse metering.AddRecordTimestampSeconds /(?<meter_time>[0-9.E]*)/
| filter (meter_time>1628492399 and meter_time<1628492400)
```
