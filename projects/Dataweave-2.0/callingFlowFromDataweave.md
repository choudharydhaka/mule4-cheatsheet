
## callingFlowViaLookup

```
%dw 2.0
/*
 * Preview won't work for this one
 */
output application/json
---
{
	explainer: "This will call a flow from dataweave itself",
	warning: "Only for very special cases!",
	
	//first param to lookup is the flow name (note: cannot be a subflow))
	//second param is a structure to go into payload
	httpIs: lookup("aSpecialLookupFlow", {protocol: "HTTP"}),
	httpsIs: lookup("aSpecialLookupFlow", {protocol: "HTTPS"}),
	sftpIs: lookup("aSpecialLookupFlow", {protocol: "SFTP"})
	
	
	/* Rant: this function is called lookup for a reason, it's 
	 * only for read type operations that you can't somehow set
	 * up before the flow (e.g. retrieve a map worth of lookup values
	 * and save in a variable, then use vars.lookuptablename.key or
	 * something
	 */
}
```
## aSpecialLookupFlow
```
<flow name="aSpecialLookupFlow" doc:id="8f5b2adb-8f75-476c-a40c-d7dc7c821600" >
		<choice doc:name="on payload protocol" doc:id="140405cb-028e-4af4-b9ce-b66c406f7788" >
			<when expression='#[payload.protocol == "HTTPS"]'>
				<set-payload value='#["SECURE HTTP"]' doc:name="SECURE HTTP" doc:id="8e53e9fd-b7ba-4aa5-a9bf-af2e4ca19fd2" />
			</when>
			<when expression='#[payload.protocol == "FTPS" or payload.protocol == "SFTP"]'>
				<set-payload value='#["SECURE File Transfer"]' doc:name='"SECURE File Transfer"' doc:id="aeb85d21-dc23-4388-b230-a7b0d84a3250" />
			</when>
			<otherwise>
				<set-payload value='#["NOT SECURE/UNKNOWN"]' doc:name="NOT SECURE/UNKNOWN" doc:id="f54cfbca-5ce5-4b2e-bde1-cc065b67be38" />
			</otherwise>
		</choice>
	</flow>
```