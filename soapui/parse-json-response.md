# Parse json response

Script assertion

```groovy
import groovy.json.JsonSlurper
def response = messageExchange.response.responseContent
def slurper = new JsonSlurper()
def json = slurper.parseText response

// Assertion example with response json
assert json.userId == 1
```
