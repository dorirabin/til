# Use threshold filter to log to different file based on level

## Background

Suppose we have this piece of code.

```java
Logger logger = LoggerFactory.getLogger("com.franzwong");
logger.info("This is info");
logger.warn("This is warn");
logger.error("This is error");
```

We want to have all warn and info level logs to be in app.log while all error logs to be in error.log.

### Expectation

app.log

```
This is info
This is warn
```

error.log

```
This is error
```

## 1st Attempt

### Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <RollingFile name="ApplicationLogFile" fileName="logs/app.log" filePattern="logs/app.%i.log">
      <PatternLayout pattern="%msg%n"/>
      <Policies>
        <SizeBasedTriggeringPolicy size="20 MB" />
      </Policies>   
    </RollingFile>
    <RollingFile name="ErrorLogFile" fileName="logs/error.log" filePattern="logs/error.%i.log">
      <PatternLayout pattern="%msg%n"/>
      <Policies>
        <SizeBasedTriggeringPolicy size="20 MB" />
      </Policies>
    </RollingFile>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="info" includeLocation="false">
      <AppenderRef ref="Console" />
    </Root>
    <Logger name="com.franzwong" level="error" includeLocation="true" additivity="false">
      <AppenderRef ref="ErrorLogFile" />
    </Logger>
    <Logger name="com.franzwong" level="info" includeLocation="true" additivity="false">
      <AppenderRef ref="ApplicationLogFile" />
    </Logger>
  </Loggers>
</Configuration>
```

### Result

app.log

```
This is info
This is warn
This is error
```

error.log

Empty

All logs go to app.log.

## 2nd Attempt

Let's try reversing the order of the loggers.

### Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <RollingFile name="ApplicationLogFile" fileName="logs/app.log" filePattern="logs/app.%i.log">
      <PatternLayout pattern="%msg%n"/>
      <Policies>
        <SizeBasedTriggeringPolicy size="20 MB" />
      </Policies>   
    </RollingFile>
    <RollingFile name="ErrorLogFile" fileName="logs/error.log" filePattern="logs/error.%i.log">
      <PatternLayout pattern="%msg%n"/>
      <Policies>
        <SizeBasedTriggeringPolicy size="20 MB" />
      </Policies>
    </RollingFile>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="info" includeLocation="false">
      <AppenderRef ref="Console" />
    </Root>
    <Logger name="com.franzwong" level="info" includeLocation="true" additivity="false">
      <AppenderRef ref="ApplicationLogFile" />
    </Logger>
    <Logger name="com.franzwong" level="error" includeLocation="true" additivity="false">
      <AppenderRef ref="ErrorLogFile" />
    </Logger>
  </Loggers>
</Configuration>
```

### Result

app.log

Empty

error.log

```
This is error
```

It looks like we cannot have more than one logger with same name. Only the one defined later will be effective.

## 3rd Attempt

Let's try using ThresholdFilter.

### Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <RollingFile name="ApplicationLogFile" fileName="logs/app.log" filePattern="logs/app.%i.log">
      <PatternLayout pattern="%msg%n"/>
      <Policies>
        <SizeBasedTriggeringPolicy size="20 MB" />
      </Policies>
      <Filters>
        <ThresholdFilter level="error" onMatch="DENY" onMismatch="ACCEPT" />
      </Filters>      
    </RollingFile>
    <RollingFile name="ErrorLogFile" fileName="logs/error.log" filePattern="logs/error.%i.log">
      <PatternLayout pattern="%msg%n"/>
      <Policies>
        <SizeBasedTriggeringPolicy size="20 MB" />
      </Policies>
      <Filters>
        <ThresholdFilter level="error" onMatch="ACCEPT" />
      </Filters>
    </RollingFile>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%msg%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="info" includeLocation="false">
      <AppenderRef ref="Console" />
    </Root>
    <Logger name="com.franzwong" level="info" includeLocation="true" additivity="false">
      <AppenderRef ref="ErrorLogFile" />
      <AppenderRef ref="ApplicationLogFile" />
    </Logger>
  </Loggers>
</Configuration>
```

### Result

app.log

```
This is info
This is warn
```

error.log

```
This is error
```

Cool. We got what we want. By using ThresholdFilter, although both appenders are triggered, the level matching controls whether the appender write to log file or not.

