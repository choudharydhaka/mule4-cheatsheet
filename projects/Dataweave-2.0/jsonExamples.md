# Json Modifier
```
2.0
/*
 *  Notice the modifiers 
 */

//for full list see: https://docs.mulesoft.com/mule-runtime/4.1/dataweave-formats#format_json
output application/json indent=true,duplicateKeyAsArray=true
---
{
	aString: "sdfasdf",
	"aKeyAsString": "asdfa",
	
	//these are nothing special in json, just showing evaluation
	("hello" ++ "there") :  "ASfds",
	"myKey$( 1 + 45 )" : "The key for this is dynamic with embedded dw script",
	
	aBoolean: true,
	
	//json drops the quotes for numbers
	aNumber: 123,
	aFloatingNumber: 123.456,
	
	//these can be culled by certain output directives
	aNull: null,
	anArrayWithANull: [1,2,null,4,5,6],
	
	//this will be turned into an array unless we change the output directive
	dupeKey: "hello",
	dupeKey: "another hello",
	
	
	innerStructure: {
		innerValue: "here"
	},
	
	//just to see the formatting
	dateTime: now(),
	
	justADate: now() as Date,
	
	//you might need to base64 encode anything fancy in the way of binaries
	aBinary: "Binaries in json are just strings" as Binary
}
```