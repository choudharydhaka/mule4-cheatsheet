# selectorsFlow
## selectors
```
dw 2.0

var myStructure = {
	nested: "top of structure",
	someOtherKey: "other key",
	someOtherKey: "other other key",
	nextLevel: {
		nested: "another value",
		down: {
			the: {
				rabbit: {
					hole: { 
						we: "go",
						nested: "white rabbit"
					}
				}
			}
		}
	}
}


output application/dw
---
{
	dotSelector: myStructure.someOtherKey,
	alternativeToDotSelector: myStructure['someOtherKey'],
	//dots as far as you need
	deepDotSelector: myStructure.nextLevel.down.the.rabbit.hole.we,
	
	//will seek out any keys called nested
	descendentSelector: myStructure..nested,
	
	//note it will only grab the first one even though there are two
	dotSelectorOnMultipleKey: myStructure.someOtherKey,
	
	//for multiple value selection for a key do this instead
	starSelector: myStructure.*someOtherKey,
	//this returns a key/value
	keyPairSelector: myStructure.&nested,
	//is a key present?
	keyPresentSelector: myStructure.nested?,
	
	//asserting presence
	keyAssertionSelector: myStructure.nested!,
	
	//using the index on an object
	indexSelectorOnObject: myStructure[1]
	
}
```
# metadataSelectors

```
dw 2.0
output text/plain encoding="UTF-8"
---
"a quick spotted quoll jumped over the lazy quokka"]' doc:name="to a string" doc:id="d73d9b02-1bbc-4342-b530-2d405b3cd769"/>
		<ee:transform doc:name="showing how to use the ^ selector" doc:id="eb2b716e-5c1e-42d9-8e27-e14ce355b290" >
			```dw 2.0
output application/json
---
{
	rawSelector: payload.^raw,
	mimeTypeSelector: payload.^mimeType,
	mediaTypeSelector: payload.^mediaType,
	encodingSelector: payload.^encoding,
	allMetaDataSelector: payload.^
	
}
```