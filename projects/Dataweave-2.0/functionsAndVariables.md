# callingCoreFunctions
```
dw 2.0
output application/json
---
{
	
	//by default the dw::core functions are imported
	//https://docs.mulesoft.com/mule-runtime/4.1/dw-core
	
	
	//these two are exactly the same thing!
	//you can call functions as function or inbetween if has two params
	concatExample: "abc" ++ "def",
	alsoConcatExample: ++("abc", "def"),
	
	//mix of maths and other stuff in there
	maxOfStuff: max( [1,2,42,3] ),
	
	//might be useful for logging/ids whatever
	uniqueID: uuid(),
	
	//getting properties from properties via the p function
	lookupOfAProperty: p("myproperty.blah"),
	
	endsWithExample: endsWith("Max the Mule", "Mule"),
	endsWithExample2: "Max the Mule" endsWith "Mule"
	
	
	
	
}
```

# simpleVariableStuff 

```dw	
dw 2.0

var myVar = "blah"

//this one makes the type explicit
var myStringVar: String = "blah also"

/** put documentation _here_ **hello** and note
 * that you can use markdown to make it pretty
 * */
var myDocumentedVar = "hi"
 
//note: you can have vars that are functions
//but it's a bit weird 
var myVarThatIsAFunction = (num1: Number, num2: Number) -> num1 + num2

output application/json
---
{
	usingMyVar: myVar,
	usingMyStringVar: myStringVar,
	usingMyDocumentedVar: myDocumentedVar,
	moreComplex: myVar ++ myStringVar,
	
	//beyond this point gets weird!
	aVarThatIsFun: myVarThatIsAFunction(123,456),
	//and just like functions that can take in two params
	//you can do "inline" form
	evenWeirder: 123 myVarThatIsAFunction 456
	
	
}
```

# functionsImportedFromModules
```dw
dw 2.0

//if you miss the * from bit then use Strings::functionName to call
import * from dw::core::Strings


//just one function from a built in module
import MD5 from dw::Crypto

//this shows how to use your own modules
//see in the src/main/resources directory
import myCustomFunction from muckaround::CustomStuff

output application/json
---
{
	//try some other fun ones: person child etc
	pluralizeExample: pluralize("octopus"),
	placedInTheRace: ordinalize(1),
	camelCase: camelize("the_quick_brown_fox"),
	forceCamel: camelize(underscore("the quick brown fox")),
	md5HashOfIt: MD5("hello" as Binary),
	//the following is using the full absolute path to the function
	sha1OfIt: dw::Crypto::SHA1("hello" as Binary),
	
	callingACustomModuleFunction: myCustomFunction("Percy the Possum")
}

```


# makingOurOwnFunctions

```
dw 2.0

//not doing much type stuff at all and has no comment documentation
//due to single line comments
fun myConcatFunction(param1, param2) = param1 ++ param2 

/** Sticks strings together but specifies the types */
fun myTypedConcatFunction(param1:String, param2:String):String = param1 ++ param2 

/** This shows off the do flow control usage to allow you to define a header and dw script */
fun myDoConcat(param1: String, param2: String):String = do {
	var theSeparator = "_"
	---
	param1 ++ theSeparator ++ param2
}


/** demonstrates how you can get more modular mapping
 * by breaking down your mappings to functions to avoid copy-paste
 */
fun mapAddress(inputAddress: Object): Object = do {
	
	
	{
		street: inputAddress.STREET,
		postCode: inputAddress.ZIP
	}
}


fun concatThatDealsWithNulls(stringOrNull1: String | Null, stringOrNull2: String | Null) = do {
	var nullValueAsString = "null"
	---
	(stringOrNull1 default nullValueAsString) ++ (stringOrNull2 default nullValueAsString)
}

/** can even use special characters for function names */
fun ++nulls(stringOrNull1: String | Null, stringOrNull2: String | Null) = concatThatDealsWithNulls(stringOrNull1, stringOrNull2)

output application/json
---
{
	/* multiline comment */
	
	callingMyFunction: myConcatFunction("hello", "max"),
	
	callingTheTypedFunction: myTypedConcatFunction("myValue", "asdf"),
	
	bewareOfNulls: myTypedConcatFunction("first", null default ''),
	
	//you can call a function as part of a string by using dollar round brackets to evaluate dw
	inLineDWScriptInAString:  "hello there $(myConcatFunction('hello','blah'))",
	
	myDoConcat: myDoConcat("xyz", "abc"),
	
	myOutputAddress: mapAddress({ STREET: "123 blah st", ZIP: "90210" }),
	
	stickingNullsTogether: concatThatDealsWithNulls("hello", null),
	//or the same thing via a different name
	stickingNullsTogether: "hi there " ++nulls null
		
	

//uh oh! Comments at the end of dataweave don't work! Unless you add a line
}
```


# varsExample

```
dw 2.0

/**
 * This var is special because it has a comment to give documentation
 */
var myVar = "hello in a var"

var inputDateFormat = "dd MMM yyyy"

//the p function grabs properties
var myVarFromProperty = p("blah.xyz")

output application/json
---
{
	usingAVar: myVar,
	myDate: "18 JAN 2018" as Date{ format: inputDateFormat},
	myPropVar: myVarFromProperty,
	
	
	//not to be confused with the flow variables from the vars dot something
	//note: by using this I needed to set up some sample data to get preview
	myOtherValue: vars.someFlowVariable 
	
}
```

# functionsToDoDateStuff

```

dw 2.0


fun convertDatesToStandardDate(inputDateString: String, 
								inputDateFormat: String): String = do
{
	var outputDateFormat = "yyyy/MM/dd"
	---
	inputDateString as Date{ format: inputDateFormat } as String { format: outputDateFormat}
}



output application/json
---
{
	
	dateStuff: convertDatesToStandardDate("2018 January 09", "yyyy MMMM dd")
	
	
	
}
```