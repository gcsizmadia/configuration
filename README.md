# Egonsoft.HU Configuration Extensions
[![GitHub](https://img.shields.io/github/license/gcsizmadia/EgonsoftHU.Extensions.Bcl?label=License)](https://opensource.org/licenses/MIT)

Extensions for Microsoft.Extensions.Configuration.

## EgonsoftHU.Extensions.Configuration.ConfigurationManager
[![Nuget](https://img.shields.io/nuget/v/EgonsoftHU.Extensions.Configuration.ConfigurationManager?label=NuGet)](https://www.nuget.org/packages/EgonsoftHU.Extensions.Configuration.ConfigurationManager)
[![Nuget](https://img.shields.io/nuget/dt/EgonsoftHU.Extensions.Configuration.ConfigurationManager?label=Downloads)](https://www.nuget.org/packages/EgonsoftHU.Extensions.Configuration.ConfigurationManager)

App.config/Web.config configuration provider implementation for Microsoft.Extensions.Configuration.

### Introduction
When migrating from .NET Framework to .NET and your `App.config` or `Web.config` file contains 
lots of settings it might be desirable to be able to incrementally transfer those settings into the 
new `appsettings.json` file so that you can incrementally replace the static uses of [`ConfigurationManager`](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.configurationmanager) with 
an injectable [`IConfiguration`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.iconfiguration) instance.

## Releases

You can download the package from **nuget.org**: [EgonsoftHU.Extensions.Configuration.ConfigurationManager](https://www.nuget.org/packages/EgonsoftHU.Extensions.Configuration.ConfigurationManager)

You can find the release notes [here](https://github.com/gcsizmadia/configuration/releases).

[Releases](RELEASES.md) under active support or development:

|Version|Release Date|Latest Patch Version|End of Support|Targets|
|-|-|-|-|-|
|10.0|2026-09-27|10.0.0|2028-11-14|`net8.0`, `net9.0`, `net10.0`<br/>`net462`<br/>`netstandard2.0`, `netstandard2.1`|

### Usage
Suppose you have an `App.config` file with the following content.
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <appSettings>
    <!-- legacy setting -->
    <add key="MyAppSetting" value="..." />
    <!--
    Temporarily added to this file during migration.
    The key uses the new format so that 
    later it can be read the same way from appsettings.json file.
    -->
    <add key="SomeApi:ApiKey" value="..." />
  </appSettings>
  <connectionStrings>
    <add name="MyConnectionString" connectionString="..." />
  </connectionStrings>
  <!-- The rest is omitted for clarity. -->
</configuration>
```
To access these settings through an `IConfiguration` instance:
- Add the configuration provider using the `IConfigurationBuilder.AddConfigurationManager()` extension method.
- After the host started, get the `IConfiguration` instance registered in the DI container.
- Now you can access your settings from the `App.config` file.
```C#
using System;

using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

HostApplicationBuilder builder = Host.CreateApplicationBuilder();

// Here you add the configuration provider.
builder.Configuration.AddConfigurationManager();

IHost host = builder.Build();

// Get the IConfiguration instance.
IConfiguration configuration = host.Services.GetRequiredService<IConfiguration>();

// Get the legacy app setting.
Console.WriteLine(configuration["MyAppSetting"]);

// Get the new setting
Console.WriteLine(configuration.GetSection("SomeApi")["ApiKey"]);

// Alternatively you can get the new setting also this way.
Console.WriteLine(configuration["SomeApi:ApiKey"]);

// Get the connection string.
Console.WriteLine(configuration.GetConnectionString("MyConnectionString"));

// Alternatively you can get the connection string also this way.
Console.WriteLine(configuration["ConnectionStrings:MyConnectionString"]);
```
### Credits
Ben Foster
- Blogpost: [Using .NET Core Configuration with legacy projects](https://benfoster.io/blog/net-core-configuration-legacy-projects/)
- Contact: [GitHub](https://github.com/benfoster), [Twitter](https://twitter.com/benfosterdev)
