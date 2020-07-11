# URLExamplesFlow
## showing Uri type and functions to parse
```
dw 2.0
import * from dw::core::URL

var uriVar = parseURI("https://docs.mulesoft.com/mule-runtime/4.1/dataweave-formats#format_csv")
output application/json
---
{
	aURIAsString: "https://docs.mulesoft.com/mule-runtime/4.1/dataweave-formats#format_csv",
	aURI: "https://docs.mulesoft.com/mule-runtime/4.1/dataweave-formats#format_csv" as Uri,
	
	//now for the bits and pieces
	bitsOfURI: {
		theHost: uriVar.host,
		isValid: uriVar.isValid,
		//watch out for this - you may need to default to 80 or 443 depending on the scheme
		port: uriVar.port,
		
		//showing how you could deal with http/https
		portWithIfLogic: if (uriVar.scheme == "http") 80 else if (uriVar.scheme == "https") 443 else null,
		
		scheme: uriVar.scheme
		
	}
}
```