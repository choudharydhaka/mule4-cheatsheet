# varsAndFunctionsFlow
## Transform Message
```  dw 2.0

var myVar = "hello"

//import a whole module means needs a prefix to get them
import dw::core::Strings

//can use the short name for this function
import MD5 from dw::Crypto

output application/json
---
{
	myFieldUsingTheVar: myVar,
	
	//this function is in the core functions
	upperCaseOfSomething: upper("hello how are you"),
	
	//using the stuff imported as a module
	camelizeExample: Strings::camelize("camel_example_here"),
	
	//can use the short function name
	md5Example: MD5("hello"),
	
	//these two are the same
	myConcatExample: "hello" ++ "goodbye",
	myAlsoConcatExample: ++("hello","goodbye"),
	
	myPluralizeExample: Strings::pluralize("person")
	
	
	
	
}
```  

# definingOurOwnFunctions
## Transform Message
```  
dw 2.0

fun mySimpleFunction(param1,param2) =   param1 ++ param2 

/** This is the docs for the function */
fun myDocumentFunction(param1 : String, param2: String) : String = param1 ++ param2 

fun myDoBlockFunction(theString: String) = do {
	var myPrefix = "PREFIX"
	---
	myPrefix ++ theString
}


output application/json
---
{
	
	firstCall: mySimpleFunction("hello", "goodbye"),
	secondCall: myDocumentFunction("aaa", "bbb")
}
```  
