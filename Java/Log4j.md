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