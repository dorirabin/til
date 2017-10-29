# Customize json deserialization

Create a class extending from `JsonDeserializer` and annotate it with `JsonComponent`.

```java
@JsonComponent
public class ToUpperCaseJsonDeserializer extends JsonDeserializer<String> {
	
	@Override
	public String deserialize(JsonParser parser, DeserializationContext context) 
			throws IOException, JsonProcessingException {
		String value = parser.getValueAsString();
		if (null != value) {
			value = value.toUpperCase();
		}
		return value;
	}

}
```
