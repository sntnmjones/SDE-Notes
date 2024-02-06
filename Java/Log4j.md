Log4j2.xml  
Very important that this file is in the `src/main/java/resources` path, otherwise it won’t be seen  
  
The `%X` will display the ThreadContext  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Configuration status="INFO">  
    <Appenders>  
        <!-- Console appender -->  
        <Console name="Console" target="SYSTEM_OUT">  
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} %X - %msg%n"/>  
        </Console>  
  
        <!-- File appender -->  
        <File name="File" fileName="logs/application.log">  
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>  
        </File>  
    </Appenders>  
  
    <Loggers>  
        <Root level="INFO">  
            <AppenderRef ref="Console"/>  
            <AppenderRef ref="File"/>  
        </Root>  
    </Loggers>  
</Configuration>
```

### Printing context at runtime
Add `%X` in log4j.xml
```xml
<Console name="Console" target="SYSTEM_OUT">
	<PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} %X - %msg%n"/>
</Console>
```

In service code
```java
ThreadContext.put("provisioningId", "1234");
LOGGER.info("hey");
ThreadContext.remove("provisioningId");

ThreadContext.put("provisioningId", "5678");
LOGGER.info("hey");
ThreadContext.remove("provisioningId");

ThreadContext.put("otherStat", "what?");
ThreadContext.put("provisioningId", "9012");
LOGGER.info("hey");
ThreadContext.clearMap();

LOGGER.info("hey");
```

Log output
```bash
12:38:50.190 [main] INFO  sandbox.logging.Main {provisioningId=1234} - hey
12:38:50.192 [main] INFO  sandbox.logging.Main {provisioningId=5678} - hey
12:38:50.192 [main] INFO  sandbox.logging.Main {otherStat=what?, provisioningId=9012} - hey
12:38:50.192 [main] INFO  sandbox.logging.Main {} - hey
```