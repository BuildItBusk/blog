---
date: '2026-03-08'
draft: false
title: 'Two C# Unit Test Tips: NullLogger and FakeTimeProvider'
description: 'Use NullLogger and FakeTimeProvider to write faster, simpler C# unit tests'
tags: ['csharp', 'unit-testing', 'dotnet']
---

## Logging

Unless you are actually testing your logging (which I mostly wouldn't recommend), you don't need to mock your own loggers. Microsoft has you covered with the [NullLogger Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.abstractions.nulllogger?view=net-10.0-pp). It is exactly what it sounds like: A mocked logger that can be injected where an `ILogger` is required, which doesn't log anything. 

Here is how to use it:

```cs
// Class which requires a logger
class LicensePlateDetector(ILogger logger)
{
    // (...)
}

// Instantiating the class in the unit test
LicensePlateDetector detector = new(NullLogger.Instance);

// Or if you need ILogger<T>
LicensePlateDetector detector = new(NullLogger<LicensePlateDetector>.Instance);
```

I have seen suggestions to default to `NullLogger` in production code using the `??` operator. I would not recommend this, as you risk silent failures if you forget to inject the actual logger.

## Dealing with delays

I recently went through our own unit tests at work. Approximately 1 minute, or 30% of the total time it takes to run the tests, were caused by waiting, for instance `Task.Delay`. This is not catastrophic, but it's also not necessary. The fix requires a little more effort than the logger, but also has a bigger impact. The solution is the [FakeTimeProvider Class](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.time.testing.faketimeprovider?view=net-10.0-pp). It works by inheriting from `TimeProvider`, so this optimization may require adjustment of both production code and tests.

Here is how to use it:

```cs
class SpeedCalculator(TimeProvider? timeProvider = null)
{
    // Unlike NullLogger, defaulting to TimeProvider.System is safe —
    // it's the real implementation, not a test double.
    private readonly TimeProvider _timeProvider = timeProvider ?? TimeProvider.System;

    public async Task Wait()
    {
        // Instead of 'Task.Delay(5000)' you can do this
        await _timeProvider.Delay(TimeSpan.FromSeconds(5));
    }
}

// When you want to test it, you use FakeTimeProvider
FakeTimeProvider fakeTime = new();
SpeedCalculator calculator = new(fakeTime);

// The 'Advance' method will forward time by 5 seconds,
// causing the waitTask to be completed instantly.
var waitTask = calculator.Wait();
fakeTime.Advance(TimeSpan.FromSeconds(5));
await waitTask;
```

This reduces a 5 second unit test to a few milliseconds. And if you have a lot of tests like this, it can add up. And more importantly, it means that developers no longer hesitate to add unit tests, because they don't want to increase the test suite duration.

A fair warning, starting to test like this may cause race conditions that you haven't seen before, where you waited. But in most cases this is caused by logic flaws in the system under test. Try to fix that, before you give up on using the `FakeTimeProvider`.

## Claude Code

You may think that Claude Code already knows this. And it does. But it does not always remember to use it. Especially not if it's not already in a lot of other tests. You can address this by putting a `CLAUDE.md` file in the root of the **test project** (not the solution root). This will be read by Claude Code every time it is working on the tests.

Here is how my `CLAUDE.md` test file looks:
````md
# Testing Timeouts

Production code that waits (`Task.Delay`, `ToPause`, etc.) must inject `TimeProvider` so tests can control time:

1. Add an optional `TimeProvider? timeProvider = null` constructor parameter; default to `TimeProvider.System`.
2. Replace `Task.Delay(duration)` with `timeProvider.Delay(duration)`.
3. In tests, pass a `FakeTimeProvider` and call `Advance()` to skip past delays instantly.

Do **not** add constructor parameters to shorten timeouts/durations for tests. Use the real production values and advance the `FakeTimeProvider` past them.

For long-running async programs, use an advancing loop:

```csharp
var fakeTime = new FakeTimeProvider();
var runTask = program.Run(settings, pts.Token, cts.Token, progress);
while (!runTask.IsCompleted)
{
    fakeTime.Advance(TimeSpan.FromSeconds(1));
    await Task.Delay(1);
}
```

The `await Task.Delay(1)` yields to let the program process the advanced time before the next tick.

# Logging in Tests

Use `NullLogger<T>.Instance` or `NullLoggerFactory.Instance` when a service requires `ILogger<T>` or `ILoggerFactory` and the test doesn't need to assert on log output. Do not stub or mock `ILogger`.
````
