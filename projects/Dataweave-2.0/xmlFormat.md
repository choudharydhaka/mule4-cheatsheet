# helloWorldXML
## 
```  
dw 2.0
output application/xml indent=true,writeDeclaration=true,encoding="UTF-8",writeNilOnNull=false
---
{
	topLevelTag: {
		aString: "hello",
		
		//notice the output of this
		anEmptyString: "",
		aNumber: 123,
		aBoolean: true,
		funnyCharacters: "stuff like < and & are escaped",
		cDataExample: "stuff like < and & are no problem for CData" as CData,

		//binary types are a bit special in xml output format
		aBinaryExample: "hello" as Binary,
		
		//skipNullOn="everywhere" or attributes or elements as the output directive modifier will change this
		//as will writeNilOnNull
		nullExample: null,
		
		//showing how attributes are created
		myInnerTag @( myAttribute: "asdf", myOtherAttribute: "asdf"): {
			blah: "sdfa"
		},
		
		//nothing special about dates in xml - will go with whatever pattern
		dateStuff: {
			aDateTime: now(),
			aDate: now() as Date			
		},
		
			
		//this would usually live inside a top level tag
		anArray: ["this", "is", "an", "array"],
		
		//more like this usually
		people: {
			person: ["Mary", "John", "Sue"]
		}
		
	}
}
```  
# xmlDealingWithMapStuff

```
  dw 2.0
output application/xml
---
{
	topLevelTag: {
		// NOTE: needed to put curly then round brackets to make this what I 
		// really wanted
		lineItems: {
			(["first", "second", "third"] map 
			lineItem: {
				lineItemIndex: $$,
				lineItemValue: $
			})
		}
	}
}
```  