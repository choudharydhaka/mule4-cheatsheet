
# arraysAndCollections
## selectorExamples

```
%dw 2.0
output application/json
---
{
	justOneIndex: [1,2,3,42,56,234,99][0],
	rangeOfValues: [1,2,3,42,56,234,99][0 to 3],
	//minus for the range selector is going to go to the end and work back
	fromTheTop: [1,2,3,42,56,234,99][3 to -1],
	justTheLastIndex: [1,2,3,42,56,234,99][-1],
	
	
	reverseAnArray: [1,2,3,42,56,234,99][-1 to 0],
	//strings are just arrays
	reverseAString: "Max the Mule"[-1 to 0],
	
	lastThreeValues: [1,2,3,42,56,234,99][-3 to -1]
	

	
}
```

## filterAndSort
```
%dw 2.0

/* Showing off sorting and filtering
 * 
 * 
 */

output application/json
---
{
		sortByNumeric: [1,2,56,3,89,5] orderBy $,
		
		//coercing numbers to strings means alphabetic sort
		alphabeticalSort: [1,2,56,3,89,5] orderBy ($ as String),
		
		//we may need to run the value through a coercion to get the type of sort required
		numericSortOfStrings: ["1","2","22","5"] orderBy ($ as Number),
		
		//showing two variations of the syntax
		filterGreaterThanTen: [1,2,56,3,89,5] filter ($ < 10),
		filterGreaterThanTen2: [1,2,56,3,89,5] filter ((item, index) -> (item < 10)),
		
		
		//we could use the index as part of the filter (although see second syntax for more sensible one)
		selectingFromAndToIndex: [1,2,56,3,89,5] filter ((item, index) -> ((index >= 3 ) and ( index <= 5))),
		selectingFromAndToIndex2: [1,2,56,3,89,5][3 to 5],
		
		
		multiSort: [{fruit:"apple", animal: "koala"}, 
					{fruit:"pear", animal: "quoll"},
					{fruit:"apple", animal: "quoll"},
					{fruit:"peach", animal: "bandicoot"}, 
					{fruit:"banana", animal: "emu"}
		] orderBy $.animal orderBy $.fruit
	
}
```
## mapExamples
```
%dw 2.0
output application/json
---
{
	firstMapSyntax: [1,2,3,42,56,234,99] map {
		theValue: $,
		theIndex: $$
	},
	
	secondMapSyntax: [1,2,3,42,56,234,99] map ((item, index) -> {
		theValue: item,
		theIndex: index
	}),
	
	
	usingStrings: ["one", "two"] map upper($),
	
	//can use the stuff in the key as well
	
	numberToStructureMap: [1,2,3,45] map ((item, index) -> {
		theIndex: index,
		
		//the next two lines do the same thing, just with inline dw
		("myKeyNumber" ++ index): "asf",
		"myInlineDWNumber$( index )" : "asdf"
	})
	
	
}
```
## simpleArrayExamples	
```
%dw 2.0
output application/json
---
{
	arrayOfNumbers: [1,2,3,42,99,123],
	
	arrayOfStrings: ["the", "quick", "Brown", "fox"],
	
	mixOfStuff: [1, "a string", null, { aStructure: "hello"}],
	
	emptyArray: []



}
```

## indexesAndRanges
```
%dw 2.0
output application/json
---
{
	//grab the first entry
	grabOneValue: [1,2,3,42,99][0],
	
	//grab the last entry uses negative numbers to do
	grabTheLastValue: [1,2,3,4][-1],
	
	//use the to and give two numbers of the indexes
	grabARange: [1,2,3,4,5,6,7][0 to 1],
	
	
	//grab from the last to the first = reversing stuff
	reverseAnArray: [1,2,3,4,5][-1 to 0],
	
	//strings are treated as arrays so you can use range selectors and indexes
	substringStuff: "The quick brown mule"[0 to 2],
	
	reverseAString: "Mule backwards is eluM"[-1 to 0]
	
	
	
}
```

## mappingArraysOrCollections
```
%dw 2.0
output application/json
---
{

// this shows that dollar is the value of the array
simpleMapWithDollarSyntax: ["hello", "gday", "hi"] map upper($),



 showingIndexAndValue: ["hello", "gday", "hi"] map {
 	theIndexIs: $$,
 	theValueIs: $
 },


 showingIndexAndValueOtherSyntax: ["hello", "gday", "hi"] map (theString, indexOfTheString) ->   {
 	theIndexIs: indexOfTheString,
 	theValueIs: theString
 }


}
```
## joinByZipAndUnzip
```
%dw 2.0

var numbers = [1,2,3,4]
var fruits = ["apples", "bananas", "pears"]


output application/json
---
{
	stuckTogetherWithUnderscores: ["first", "second", "third"] joinBy "_",
	
	
	//zip and unzip operate on arrays
	zipNumbersWithFruits: zip(numbers, fruits),
	
	unzipExample: unzip([["a",1], ["b",2]])
	
	
	
}
```

## sortingViaOrderByAndFiltering
```
%dw 2.0
output application/json
---
{
	// two variations of the same thing
	numberSortingExample: [99,1,2,56,6,345,7,88,888,9] orderBy ((item, index) -> (item) ),
	numberSortingExample2: [99,1,2,56,6,345,7,88,888,9] orderBy ($),
	
	//note: by making the type a string it will alphabetical sort
	alphabeticalSort: ["apple", "tomato", "avocado", "zukini", "pear"] orderBy $,
	aphabeticalSortOfNumbers: [1,45,77,11,2,99,12931] orderBy ($ as String),
	
	
	//for sorting of multiple fields - the most important one is last, least important first
	multiOrdering: [ {
		col1: "zzz",
		col2: "hello"
	},  {
		col1: "aaa",
		col2: "hello"
	},
	 {
		col1: "bbb",
		col2: "goodbye"
	}, {
		col1: "aaa",
		col2: "apple"
	} ] orderBy ($.col1) orderBy ($.col2),
	
	
	//filtering examples (which are same thing)
	filterLowNumbers: [1,2,500,56,99,2342,67] filter ((item, index) -> ( item > 100 )),
	filterLowNumbers2: [1,2,500,56,99,2342,67] filter ( $ > 100 )
	
	
}
```
