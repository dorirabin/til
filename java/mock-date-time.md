# Mock date time

Java 8 provides `Clock` class which allows us to get fixed date time value.

```java
// Setup Clock
Clock clock = Clock.fixed(Instant.parse("2016-03-05T08:30:00Z"), ZoneId.of("+8"));

// Get 'current time'
LocalDateTime dateTime = LocalDateTime.now(clock);
String time = dateTime.format(DateTimeFormatter.ofPattern("uuuu-MM-dd HH:mm:ss"));

// Assert with expected value
Assert.assertEquals("2016-03-05 16:30:00", time);
```