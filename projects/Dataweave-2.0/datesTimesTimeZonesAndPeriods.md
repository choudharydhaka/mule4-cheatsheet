# datesTimesTimeZonesAndPeriods


<flow name="workingWithDateTimes" doc:id="f1a17e02-02b4-4433-8f3c-ca3eb5c9f86f" >
		<ee:transform doc:name="Transform Message" doc:id="00642a89-1571-46ca-a42a-38c9ee2bee25" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/json
---
{
	//yyyy-MM-dd is expected in pipe characters for 
	aDate: |2018-11-29|,
	
	//HH:mm:SS for times
	aTime: |23:59:30|,
	
	//tip: use the syntax error message to work out the pattern
	aDateTime: |2018-11-29T17:23:50|,
	
	//we can pull bits back out
	gettingTheYear: |2018-11-29|.year,
	
	//now function from core module
	nowIsTheDateTime: now(),
	nowAsDate: now() as Date,
	nowAsTime: now() as Time,
	nowAsLocalTime: now() as LocalTime,
	todaysMonth: now().month,
	
	
	aPeriod: |P3M|,
	
	//you can add period types which are of the form |PsomthingTsomethingElse|
	fiveDaysFromToday: now() + |P5D|,
	fiveHoursFromNow: now() + |PT5H|,
	fiveHoursAnd3DaysFromNow: now() + |P3DT5H|,
	
	
	//3 months is a period of 3M note: can have M, D, Y
	threeMonthsAgo: now() - |P3M|,
	threeHoursFromNow: now() + |PT3H|,
	
	
	differenceBetweenTimes: |23:59:59| - |09:00:00|,
	
	//this will only go as precise as you go to
	differenceNowBackToY2K: (now() as Date) - |2000-01-01|,
	differenceNowBackToY2KToTheMillisecond:  now() - |2000-01-01T00:00:00Z|,
	
	
	//this will only return units in the period that exist
	//there are functions to calculate days between and such
	bewarePeriodObjectsAreNotMagic: |P3M|.days
}]]></ee:set-payload>
			</ee:message>
		</ee:transform>
	</flow>
## theDwCoreBuiltInDateTimeFunctions
```
%dw 2.0
output application/json
---
{
	daysBetweenYearNYE2k: daysBetween(|2000-01-01|, now()),
	
	isThisYearALeapYear: isLeapYear( now().year),
	typeOfStuff: {
		typeOfNow: typeOf( now()),
		typeOfDate: typeOf(|2019-01-25|),
		typeOfLocalTime: typeOf( |23:59:59|)
	},
	
	//operators
	addingDateTimeAndPeriod: now() + |P1Y1M1D|,
	subtractingTimeAndPeriod: |23:59:59| - |PT3H|
}
```	
## convertingDatesAndTimes
```
%dw 2.0

type MyDate = String { format: "yyyy/MMM/dd"}

output application/json
---
{
	nowIsTheDateTime: now(),
	nowAsMyStringDate: now() as String { format: "d MMMM yyyy"},
	stringIntoADate: "2018/12/08" as Date { format: "yyyy/MM/dd"},
	
	//double coercion is for converting strings to other strings
	stringDateIntoOtherStringDate: "1 December 2018" 
		as Date { format: "d MMMM yyyy"} 
		as String { format: "yyyy/MMM/dd"},
	
	//note: only during changes to/from different types will it do stuff
	stringDateToStringDate: "16 November 18" as Date { format: "dd MMMM yy"}
		as String { format: "yyyy-MMM-dd"},
		
	usingAType: now() as MyDate,
	
	//using period to add or remove time/days etc
	adding3DaysToNow: now() + |P3D|,
	adding3HoursToNow: now() + |PT3H|,
	
	//also a core function to do stuff
	differenceBetween: daysBetween(now(), |2025-01-01|)
	
}
```
## timeZones
    
```
                %dw 2.0
/*
 * Moving dates around timezones and other stuff
 * try flicking between application/java and application/dw
 */
 
var aetTimeZone = ("AET" as TimeZone)
 
output application/java
---
{
	//now uses the server timezone
	nowByItself: now(),
	
	//Strings can be coerced into TimeZones
	//GMT or Zulu time
	nowShiftedTimeZoneToGMT: now() >> ("GMT" as TimeZone),
	//aussie time
	nowShiftedTimeZoneToAET: now() >> ("AET" as TimeZone),
	nowShiftedUsingVarTimeZone: now() >> aetTimeZone,
	
	//timezones can be numeric careful to put in the plus or minus!
	aTimeZoneInAustralia: |+10:00|,

	//if you know a local date time is actuall from a zone you can
	//plus plus it to return the zoned version
	addingATimezoneToLocalDate: |2019-02-21| ++ ("AET" as TimeZone),
	//same with times
	addingTimezoneToLocalTime: |23:59:59| ++ |+10:00|
	
	
}```
## periods
```
%dw 2.0

var numberOfDays = 56
var numberOfHours = 78

output application/java
---
{
	//can use a string to build one up
	creatingAPeriodFromAString: "P$(numberOfDays as String)D" as Period
}
```