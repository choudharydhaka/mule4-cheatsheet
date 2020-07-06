
# csvExamples

## helloWorldCSV

```

%dw 2.0
//separator examples \t , . |
output application/csv header=true,separator=",",escape="\\"
---
[{
	col1: "hello",
	col2: "there",
	col3: 123,
	col4: "has,a,comma,or,few"
},
{
	col1:"blah",
	// this wont work too wellcol56: "asd"
	col2: null,
	col3:  "aadf",
	col4: null
},
{
	col1: "afterTheBlank",
	col2: "asdf",
	col3: "asdf"
}
]
```
## tabDelimitedExample

```
%dw 2.0

output application/csv header=true,separator="\t"
---
[{
	col1: "hello",
	col2: "there",
	col3: 123,
	col4: "has a \t tab in it"
},
{
	col1:"blah",
	col2: null,
	col3:  "aadf",
	col4: null
},
{
	col1: "afterTheBlank",
	col2: "asdf",
	col3: "asdf"
}
]
```


## helloPipeDelimited

```
%dw 2.0
//this format is actually not just CSV as we can change the separators
output application/csv header=true,separator="|",lineSeparator="\n"
---
[{
	col1: "hello",
	col2: "max mule"
},
{
	col1: "hello2",
	col2: "maxine mule"
}
]```


## csvExamplesFlow

```
%dw 2.0

var "first|second|third\n" ++ "a|b|c\n" ++ "aa|bb|cc"

output application/json
---
payload
```