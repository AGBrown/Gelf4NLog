# Gelf4NLog
Gelf4NLog is an [NLog] target implementation to push log messages to [GrayLog2]. It implements the [Gelf] specification and communicates with GrayLog server via UDP.

## Solution
Solution is comprised of 3 projects: *Target* is the actual NLog target implementation, *UnitTest* contains the unit tests for the NLog target, and *ConsoleRunner* is a simple console project created in order to demonstrate the library usage.
## Usage
Use Nuget:
```
PM> Install-Package Gelf4NLog.Target
```
### Configuration
Here is a sample nlog configuration snippet:
```xml
<configSections>
  <section name="nlog" type="NLog.Config.ConfigSectionHandler, NLog" />
</configSections>

<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

	<extensions>
	  <add assembly="Gelf4NLog.Target"/>
	</extensions>

	<targets>
	  <!-- Other targets (e.g. console) -->
    
	  <target name="graylog" 
			  xsi:type="graylog" 
			  hostip="192.168.1.7" 
			  hostport="12201" 
			  facility="console-runner"
	  />
	</targets>

	<rules>
	  <logger name="*" minlevel="Debug" writeTo="graylog" />
	</rules>

</nlog>
```

Options are the following:
* __name:__ arbitrary name given to the target
* __type:__ set this to "graylog"
* __hostip:__ IP address of the GrayLog2 server
* __hostport:__ Port number that GrayLog2 server is listening on
* __facility:__ The graylog2 facility to send log messages
* __AdditionalFields:__ Any additional fields to add to every message (see below)

### Code

```c#
//excerpt from ConsoleRunner
var eventInfo = new LogEventInfo
    			{
					Message = comic.Title,
					Level = LogLevel.Info,
				};
eventInfo.Properties.Add("Publisher", comic.Publisher);
eventInfo.Properties.Add("ReleaseDate", comic.ReleaseDate);
Logger.Log(eventInfo);
```

## Additional Properties

There are several ways that additional properties can be added to a log.

### Configuration

Any static information can be set through configuration by adding a comma-separated list of key:value pairs.

```
	  <target name="graylog" 
			  xsi:type="graylog" 
			  hostip="192.168.1.7" 
			  hostport="12201" 
			  facility="console-runner"
			  AdditionalFields="app:RandomSentence,version:1.0"
	  />
```

This will add the following fields to your GELF log:

```
{
    ...
	"_app":"RandomSentence",
	"_version":"1.0",
	...
}
```

You can also use your own custom field and key/value separators to deal with the case when the additional fields contain commas or colons
```
	  <target name="graylog" 
			  xsi:type="graylog" 
			  hostip="192.168.1.7" 
			  hostport="12201" 
			  facility="console-runner"
			  AdditionalFields="app¬:¬RandomSentence¬|¬version¬:¬1.0"
			  FieldSeparator="¬|¬"
			  KeyValueSeparator="¬:¬"
	  />
```

### LogEventInfo Properties

You can use `LogEventInfo.Properties` to log additional fields to the output. Here is an example from the `ConsoleRunner`:

```c#
//excerpt from ConsoleRunner
var eventInfo = new LogEventInfo {
					Message = comic.Title,
					Level = LogLevel.Info,
				};
eventInfo.Properties.Add("Publisher", comic.Publisher);
eventInfo.Properties.Add("ReleaseDate", comic.ReleaseDate);
Logger.Log(eventInfo);
```


[NLog]: http://nlog-project.org/
[GrayLog2]: http://graylog2.org/
[Gelf]: http://graylog2.org/about/gelf

##Contributing
Would you be interested in contributing? All PRs are welcome but also see issue [#12](../../issues/12).
