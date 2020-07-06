# binaries
## binariesExamplesAndFunctions

```
%dw 2.0

var inputBinary = "Hello max the mule" as Binary

import toBase64,toHex from dw::core::Binaries

output application/java
---
{
	simpleBinary: inputBinary,
	asBase64: toBase64(inputBinary),
	asHex: toHex(inputBinary)
}
```

## binaryXML
```
%dw 2.0
output application/xml
---

//binary as an attribute doesn't mean anything
binaryExamples @(binaryAttribute: "hello" as Binary):{
	
	//this will be encoded
	aBinary: "The Quick brown fox" as Binary,
	
	//an alternative is to use CData
	aCData: "Jumped over the lazy red dog" as CData
}

```