# helloYaml
```
dw 2.0
output application/yaml
---
{
	hello: "blah",
	
	//the nested key should indent
	nestedKey: {
		nestedValue: "nested under nested key",
		aNumber: 1234,
		aBoolean: true
	}
}
``` 