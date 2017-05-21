# Custom pattern converter to output Spring properties

We can create a custom pattern converter to output the value from Spring properties.

## Application context holder

Since the pattern converter is not Spring-managed bean, we need to provide the application context to the pattern converter to get the Spring properties.

```java
@Component
public class ApplicationContextHolder implements ApplicationContextAware {
  
  private static ApplicationContext context;

  @Override
  public void setApplicationContext(ApplicationContext context) throws BeansException {
    ApplicationContextHolder.context = context;
  }
  
  public static ApplicationContext getContext() {
    return context;
  }
}
```

## Pattern converter

Make sure the values used in ```@ConverterKeys``` are unique to Log4j 2.

Log4j 2 also requires pattern converter to have 
a static ```newInstance``` method (with String array as paramter).

```java
@Plugin(name = "SpringPropsPatternConverter", category = PatternConverter.CATEGORY)
@ConverterKeys({ "springProps" })
public class SpringPropsPatternConverter extends LogEventPatternConverter {

  private String propName;

  private String propValue;

  protected SpringPropsPatternConverter(String propName) {
    super("SpringProps", "springProps");
    this.propName = propName;
  }

  public static SpringPropsPatternConverter newInstance(String[] options) {
    if (options == null || options.length == 0) {
      throw new IllegalArgumentException("options must contain value");
    }
    return new SpringPropsPatternConverter(options[0]);
  }

  @Override
  public void format(LogEvent event, StringBuilder toAppendTo) {
    if (null == propValue) {
      propValue = ApplicationContextHolder
          .getContext()
          .getBean(Environment.class)
          .getProperty(propName);
    }
    toAppendTo.append(propValue);
  }

  @Override
  public boolean handlesThrowable() {
    return false;
  }

}

```

## Log4j 2 configuration

Apart the pattern, we also need to put the package where our pattern converter is to the attribute ```packages``` of ```Configuration``` tag.


```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration packages="com.franzwong.til.logging.log4j2">
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%springProps{appName}|%m%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="info" includeLocation="false">
      <AppenderRef ref="Console" />
    </Root>
  </Loggers>
</Configuration>
```

## Spring properties

```
appName=My application
```

## Application

You can just use it in normal way.

```java
Logger logger = LoggerFactory.getLogger(getClass());
logger.info("Hello world");
```

## Result

```
My application|Hello world
```