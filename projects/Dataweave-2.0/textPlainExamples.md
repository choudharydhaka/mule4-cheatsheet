# textPlainStuffFlow
## Transform Message
```
dw 2.0
output text/plain 
---
//whatever I do needs to be a string at the end of the day
"this is ok " ++ " so is this " ++ (now() as String)
```

# helloWorldTextPlain
## Transform Message
			```dw 2.0
/**
 * The dataweave script has to evaluate to a string, one way or the other
 * This example shows a variety of things to produce the end result
 */
output text/plain
---
"hello world" ++ 
" how are you" ++ 
//this bit shows the use of an inline dataweave script
//it might make for more elegant code in some cases
" the time is $( now() as String )" ++ 
//just like java you can put in newline characters via escaping
"\n" ++ 
"this should be on the next line"
```