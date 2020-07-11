# additionSubtractionMerging
## Transform Message
```
dw 2.0

var maxObject =  {
		firstName: "Max",
		lastName: "Mule"
}

var maxineObject = {
	firstName: "Maxine",
	lastName: "Mule"
	
}


import mergeWith from dw::core::Objects

output application/json
---
{
	//combines the two, keeping all fields
	addingTwoObjects: maxObject ++ maxineObject,
	
	
	//the second of the objects will win for any overlapping fields, so maxine wins over max
	mergingTwoObjects: mergeWith(maxObject, maxineObject),
	//alternative syntax
	//mergingTwoObjects2: maxObject mergeWith maxineObject,
	
	
	//any exactly matching key/value combinations will be removed
	subtractingTwoObjects: maxObject -- maxineObject
	
}
```

# addAndSubtract
## plus plus objects
```
dw 2.0
output application/json
---
{
	firstName: "max",
	lastName: "mule",
	dob: "1/1/2006"
}

/* this will add the fields in to the structure*/
++ 
{ 
	vipStatus: "GOLD",
	dob: "NEW VAlue"
}
```
## minus minus an array
```
dw 2.0
output application/json
---
{
	firstName: "max",
	lastName: "mule",
	dob: "1/1/2006"
}

//this will remove the fields with the key names in an array
-- ["dob", "otherFieldThatIsntThere"]]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		## replacing fields
			```dw 2.0

/*
 * An alternative to this would be to use mapObject
 */

output application/json
---
{
	firstName: "max",
	lastName: "mule",
	dob: "1/1/2006"
}
//first cull the fields you want to replace
-- 
["vipStatus", "dob"] 

/* this will add the fields/values in to the structure*/
++ 
{ 
	vipStatus: "GOLD",
	dob: "NEW VAlue"
}
```    

## can subtract fields
```
dw 2.0
output application/json
---
{
	firstName: "max",
	lastName: "mule",
	dob: "1/1/2006"
}
//this will remove the dob key/value
- "dob"
```