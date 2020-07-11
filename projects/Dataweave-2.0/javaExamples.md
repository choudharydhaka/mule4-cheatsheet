# create a java object of a certain type by coercing to Object and feeding in the class

 ```
 dw 2.0
/* This demonstrates how dataweave can be used
 * to create a java object of a certain type by
 * coercing to Object and feeding in the class, 
 * it will then use getters/setters on it
 */
output application/java
---
{
	//this will call setName method
	name: "Quinny The Quokka",
	//this will call setSize 
	size: "Small"
} as Object {
	class : "com.mulesoft.dataweave.muckaround.Quokka"
}
```
	
# for calling static library type methods via import and constructor via new
```
dw 2.0
/**
 * The following imports tell dataweave a class exists and can
 * be treated just like a dw function module
 */
import java!com::mulesoft::dataweave::muckaround::StaticMethodsExample

import java!com::mulesoft::dataweave::muckaround::Quokka
output application/json
---
{
	//here it is called just like a dw function
	threeAdded: StaticMethodsExample::addThreeNumbers(1,2,3),
	
	//using the new function will invoke the constructor
	creatingANewObject: Quokka::new("Quincy the Quokka","tiny")
}
```
	
# taking in or returning java objects of a certain class

```  dw 2.0

/** Shows how you can be precise about what a function will return
 * notice the Object has a metadata schema format property of what class
 * it won't 
 */

fun getQuokka() = { name:"Quincey the Quokka" } as Object {
	class : "com.mulesoft.dataweave.muckaround.Quokka"
}

/** This will return an object of type Mule
 */
fun getAMule(fName: String, lName: String): Object  { class: "com.mulesoft.dataweave.muckaround.Mule" } = {
	firstName: fName,
	lastName:  lName
} as Object { class: "com.mulesoft.dataweave.muckaround.Mule" }

 
fun takeInAMuleAndReturnTheName( theMule: Object { class: "com.mulesoft.dataweave.muckaround.Mule" } ): String = do {
	(theMule.firstName as String default "") ++ " " ++ (theMule.lastName as String default "")
}


var exampleMule = ( { firstName: "Data", lastName:"Weave"} as Object{ class: "com.mulesoft.dataweave.muckaround.Mule" })

output application/java
---
{
	quokka: getQuokka(),
	
	//firstMule: getAMule(null,null),
	secondMule: getAMule("Max", "Mule"),
	//nameOfMule: takeInAMuleAndReturnTheName( getAMule("Max", "Mule") ),
	nameOfOtherMule: takeInAMuleAndReturnTheName(exampleMule)
	
}
```
	
# dw types using a java class

```
dw 2.0

/* we can pre-bake the java class stuff as a dw type */

type Mule = Object { class: "com.mulesoft.dataweave.muckaround.Mule" }
type Quokka = Object { class: "com.mulesoft.dataweave.muckaround.Quokka" }

output application/java
---
{
	aMule: { firstName: "Maxx", lastName: "De Mule"} as Mule,
	aQuokka: {name: "Quokk", size: "smallish"} as Quokka,
	
	//with defined types you will get that name back from typeOf
	theType: typeOf( {name: "Quokk", size: "smallish"} as Quokka ) as String
}
```