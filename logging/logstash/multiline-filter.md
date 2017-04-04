# Multiline filter

The multiline filter is used to merge multiple log lines into a single event.

Let's see an example. We are going to group the following lines into 2 events. Line 1, 2, 3 should be in one event, and line 4, 5, 6 should be in another.

```
2016-02-27 Line 1
  Line 2
  Line 3
2016-02-27 Line 4
  Line 5
  Line 6  
```

If the `pattern` property is matched, logstash will try to merge this line with `previous` or `next` event stated by `what` property. That means the `pattern` property should not match the "delimiter" of the event, which is the date in Line 2 and Line 4 this example.

The following configuration can be read as "If line starts with whitespace, this line will merge with the previous line, otherwise it is treated as a separate event".

```
multiline {
  pattern => "^\s+"
  what => "previous"
}
```