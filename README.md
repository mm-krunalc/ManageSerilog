# Serilog, Serilog With MSSqlServer, Serilog Seq Using Core 5
Implement demo application that contains Serilog, along with database logging and serilog sink seq.
## Required Packages:
* [Serilog.AspNetCore](https://www.nuget.org/packages/Serilog.AspNetCore/)
* [Serilog.Sinks.MSSqlServer](https://www.nuget.org/packages/Serilog.Sinks.MSSqlServer/)
* [Serilog.Sinks.Seq](https://www.nuget.org/packages/Serilog.Sinks.Seq/)

## Configuration Code
### Program.cs file
```cs
public class Program {
	public static void Main(string[] args)
	{
		CreateHostBuilder(args).Build().Run();
	}
	public static IHostBuilder CreateHostBuilder(string[] args) => 
		Host.CreateDefaultBuilder(args)
		.ConfigureWebHostDefaults(webBuilder =>
		{
			webBuilder.UseStartup<Startup>();
		})
	    .UseSerilog((hostingContext, LoggerConfiguration) =>
		{
			LoggerConfiguration.ReadFrom.Configuration(hostingContext.Configuration);
		});
	}
```

### appsettings.json
```json
"Serilog": {
	"MinimumLevel": {
		"Default": "Information",
		"Overrides": {
			"Microsoft": "Information",
			"Microsoft.Hosting.LIfetime": "Information"
		}
	},
	"Using": [ "Serilog.Sinks.Seq", "Serilog.Sinks.MSSqlServer" ],
	"WriteTo": [
	    // Show Logs to on Seq UI.
		{
			"Name": "Seq", // write to serilog sink.
			"Args": {
				"serverUrl": "http://localhost:5341" // server url OR local Url.
			}
		},
		// Add Logs in SQL table.
		{
			"Name": "MSSqlServer", // write to MS Sql.
			"connectionString": "", // put your DB connection string here.
			"tableName": "tblLogs" // SQL table name.
		},
		// Add logs to text file.
		{
			"Name": "File", // write to file.
			"Args": {
				"path": "log.txt", // log text file name.
				"rollingInterval": "Day"
			}
		}
	]
}
```
## External application to `RUN` Serilog Sink:
[Download Seq Services Administration](https://datalust.co/download)