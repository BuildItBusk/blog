---
date: '2025-04-15T10:38:08+02:00'
draft: false
title: 'Background Processing With Channels'
description: 'Learn how to combine .NET Channels and BackgroundService to process real-time data efficiently without missing entries.'
tags: ['dotnet', 'csharp', 'channels', 'background-service', 'async']
---

I recently came across a scenario, where I had to process a lot of incoming data in real time, where missing a single entry is not an option. At the same time I wanted to disconnect the class which was fetching data (the collector) from the class which were processing the data (the processor). The result was a combination of [Channels](https://learn.microsoft.com/en-us/dotnet/core/extensions/channels) and the [BackgroundService](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/host/hosted-services).

Before we look at the code, you will need the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/microsoft.extensions.hosting) Nuget package. You can install it with:

```
dotnet add package Microsoft.Extensions.Hosting
```

When that is done, you need to add two new classes to your project, a *collector* and a *processor*.

**Collector.cs**
```cs
class Collector
{
    private readonly Channel<string> _channel;

    public Collector(Channel<string> channel)
    {
        _channel = channel;
    }

    // In my scenario Collect is triggered by an event from another part of
    // the system, but there are many options here.
    internal void Collect(string data)
    {
        if (!_channel.Writer.TryWrite(data))
        {
            Console.WriteLine("Failed to write to channel.");
        }
    }
}
```

**Processor.cs**
```cs 
class Processor : BackgroundService
{
    private readonly ChannelReader<string> _reader;
    
    public Processor(Channel<string> channel)
    {
        _reader = channel.Reader;
    }

    protected override async Task ExecuteAsync(CancellationToken ct)
    {
        // Check the official documentation for different consumer patterns
        // depending on whether your incoming data is finite or not.
        while (!ct.IsCancellationRequested)
        {
            var data = await _reader.ReadAsync(ct);
            Console.WriteLine($"Processing: {data}");
        }
    }
}
```

Obviously this is over simplified, but take it as a template which you can use to get started. There are too many different use cases to cover everything in this post.

One thing we do need to cover, is how to use it though. For a console application, you will have to instantiate the *collector* and *processor* manually and hook them up to the same channel (which you also have to instantiate).

**Console application**
```cs
using System.Threading.Channels;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var channel = Channel.CreateUnbounded<string>();
var collector = new Collector(channel);

// A BackgroundService is intended to run as a service (which the name kinda suggests).
var host = Host.CreateDefaultBuilder(args)
            .ConfigureServices(services =>
            {
                services.AddSingleton(channel);
                services.AddHostedService<Processor>();
            })
            .Build()
            .RunAsync();

// Will print 'Processing Data 0, Processing Data 1, ...' in the console.
for (var i = 0; i < 10; i++)
{
    collector.Collect($"Data {i}");
}

// Awaits for the background host to finish. Without this the program will return and end prematurely.
await host;
```

For an ASP.NET Web App it looks quite similar. I would recommend always registering the channel first, to ensure that both of the other classes retrieves the same instance of it.

**ASP.NET Web Application**
```cs
var builder = WebApplication.CreateBuilder(args);
{
    var channel = Channel.CreateUnbounded<string>();
    builder.Services.AddSingleton(channel);
    builder.Services.AddHostedService<Processor>();
    builder.Services.AddSingleton<Collector>();
    //...register other dependencies
}
```

That is all it takes to get started with channels and background services. If you are interested in a bit more details I would recommend reading the official documentation as well as [Nick Chapsas video on Getting Started With Channels](https://youtu.be/lHC38t1w9Nc?si=CUEOwRBR8794A2ky).

# FAQ
*Why do I need a collector, couldn't I just write directly to the channel?*
Yes, in this very simple case you could. I introduced a collector, because I had to do some data transformation and mapping, before adding it to the queue. If your use case really is as simple as above, feel free to skip the collector.

*Why should I use a background service for processing?*
If you have a process which is asynchronous and long running a background service is a great abstraction. The combination with channels is very performant and allows a buffer, in case of temporary delays in the processing.