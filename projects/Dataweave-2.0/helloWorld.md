# helloDataFields
		
```
dw 2.0

/** Refer to https://docs.mulesoft.com/mule-runtime/4.1/dataweave-types for info on types
 *  Toggle the output between application/java, application/dw, application/json
 */
output application/java
---
{
	hi: "hello",
	goodbye: "bye",
	myIntNumber: 12,
	myFloatNumber: 12.34,
	myVeryBigNumber: 12312312312312312312,
	myBoolean: true,
	myNullValue: null,
	
	//hardwired date/time formats can be found by writing the pipe characters then
	//looking at the error message (or look at the docs!)
	myDate: |2018-11-15|,
	myDateTime: |2018-11-15T23:59:59|,
	todaysDateTime: now(),

	//URI types can be pulled apart, but otherwise look like strings for most outputs
	aURI: "https://docs.mulesoft.com/mule-runtime/4.1/dataweave" as Uri,

	//arrays can be mixed types
	myArray: [1,2,3,4,5,99],
	myMixedArray: [1, "hello", { hi: "there"}],
	
	mySubStructure: {
		myInnerKey: "hello inside a structure"
	}
}
```
# numbersAndFormatting
		
```
dw 2.0
output application/json
---
{
	aNumber: 123,
	aFloatingNumber: 12.345,
	stringThatIsNumber: "1234",
	stringToNumber: "1234" as Number,
	numberToString: 1234 as String,
	//this stuff uses java number formatter characters
	//this example will keep/round to the number of hashes after the dot
	numberToStringWithFormat: 1234.56 as String { format: "#,###.###"},
	
	//this one puts in zeros when does not have enough decimals
	moreFormattingStuff: 123.4 as String{ format: "#.00"},
	
	localeExample: 34123.45 as String{ format: "EUR #,###.00", locale: "ES"}
}
```