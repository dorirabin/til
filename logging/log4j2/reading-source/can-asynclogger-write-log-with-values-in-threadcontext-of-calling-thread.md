# Can AsyncLogger write log with values in ThreadContext of calling thread?

```AsyncLogger``` can write log in asynchronous way. That means it writes log in another thread which may be different to the calling thread. However, ```ThreadContext``` is bound to the calling thread. Can ```AsyncLogger``` write log with information inside ```ThreadContext```?

## Reading source

```AsyncLoggerConfig``` extends ```LoggerConfig```. LoggerConfig uses either ```DefaultLogEventFactory``` or ```ReusableLogEventFactory``` to create log event.

```java
public class LoggerConfig extends AbstractFilterable {
  static {
    // ...
    if (LOG_EVENT_FACTORY == null) {
      LOG_EVENT_FACTORY = Constants.ENABLE_THREADLOCALS
              ? new ReusableLogEventFactory()
              : new DefaultLogEventFactory();
    }
  }

  public LoggerConfig() {
    this.logEventFactory = LOG_EVENT_FACTORY;
    // ...
  }

  public void log(final String loggerName, final String fqcn, final Marker marker, final Level level,
          final Message data, final Throwable t) {
    // ...
    final LogEvent logEvent = logEventFactory.createEvent(loggerName, marker, fqcn, level, data, props, t);
    // ...
  }
}
```

### DefaultLogEventFactory

```DefaultLogEventFactory``` will create a ```Log4jLogEvent``` object.

```java
public class DefaultLogEventFactory implements LogEventFactory {
  @Override
  public LogEvent createEvent(final String loggerName, final Marker marker,
                              final String fqcn, final Level level, final Message data,
                              final List<Property> properties, final Throwable t) {
    return new Log4jLogEvent(loggerName, marker, fqcn, level, data, properties, t);
  }
}
```

Inside ```Log4jLogEvent```, it uses ```ContextDataInjector``` which is a ```ThreadContextDataInjector```. This injector will make a copy of ```ThreadContext```, so ```Log4jLogEvent``` will contain a snapshot of ```ThreadContext```.

```java
public class Log4jLogEvent implements LogEvent {

  private static final ContextDataInjector CONTEXT_DATA_INJECTOR = ContextDataInjectorFactory.createInjector();

  public Log4jLogEvent(final String loggerName, final Marker marker, final String loggerFQCN, final Level level,
                      final Message message, final List<Property> properties, final Throwable t) {
     this(loggerName, marker, loggerFQCN, level, message, t, null, createContextData(properties),
         ThreadContext.getDepth() == 0 ? null : ThreadContext.cloneStack(), // mutable copy
         0, // thread name
         null, // stack trace element
         0,
         null, // LOG4J2-628 use log4j.Clock for timestamps
         // LOG4J2-744 unless TimestampMessage already has one
         message instanceof TimestampMessage ? ((TimestampMessage) message).getTimestamp() :
             CLOCK.currentTimeMillis(), nanoClock.nanoTime());
  }

  private static StringMap createContextData(final List<Property> properties) {
      final StringMap reusable = ContextDataFactory.createContextData();
      return CONTEXT_DATA_INJECTOR.injectContextData(properties, reusable);
  }
}
```

### ReusableLogEventFactory

```ReusableLogEventFactory``` also uses a ```ThreadContextDataInjector``` to make a copy of ```ThreadContext```

```java
public class ReusableLogEventFactory implements LogEventFactory {

  private final ContextDataInjector injector = ContextDataInjectorFactory.createInjector();

  @Override
  public LogEvent createEvent(final String loggerName, final Marker marker,
                              final String fqcn, final Level level, final Message message,
                              final List<Property> properties, final Throwable t) {
    // ...
    result.setContextData(injector.injectContextData(properties, (StringMap) result.getContextData()));
    // ...
  }
}
```

From the source code, we can see the snapshot of ```ThreadContext``` stored in ```LogEvent```, so other threads are able to access to the values inside ```ThreadContext``` of the calling thread.
