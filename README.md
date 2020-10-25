# Joint.CQRS.Events

## Events

Adds ability to create and process **events** in the sense of [CQRS](https://martinfowler.com/bliki/CQRS.html).

## Installation

```
dotnet add package Joint.CQRS.Envents
```

## Dependencies

- [Joint](https://www.nuget.org/packages/Joint/)

## Usage

Implement `IEvent` or `IRejectedEvent` (marker) interface in the selected class. Since the event represents something that already happened, you should follow the convention:

- keep all the events **immutable**
- name of your events should be **past tense**

```c#
public class AccountCreated : IEvent
{
    public Guid Id { get; }

    public AccountCreated(id)
    {
        Id = id;
    }
}
```

Create dedicated **event handler** class that implements `IEventHandler<TEvent>` interface with `HandleAsync()` method:

```c#
public class AccountCreatedHandler : IEventHandler<AccountCreated>
{
    public Task HandleAsync(AccountCreated @event)
    {
        //put the handling code here
    }
}
```

You can easily register all event handlers in DI container by calling `AddEventHandlers()` method on `IJointBuilder`:

```c#
public IServiceProvider ConfigureServices(this IServiceCollection services)
{
    var builder = JointBuilder
        .Create(services)
        .AddEventHandlers();

    //other registrations
    return builder.Build();
}
```

Dispatching a particular event object can be also done using Joint package. Start with registering in-memory dispatcher on your `IJointBuilder` by calling a `AddInMemoryEventDispatcher()` method:

```c#
public IServiceProvider ConfigureServices(this IServiceCollection services)
{
    services.AddOpenTracing();

    var builder = JointBuilder
        .Create(services)
        .AddCommandHandlers()
        .AddInMemoryEventDispatcher();

    //other registrations
    return builder.Build();
}
```

Then simply inject `IEventDispatcher` into a class and call `PublishAsync()` method:

```c#
public class AccountsService
{
    private readonly IEventDispatcher _dispatcher;

    public AccountsService(IEventDispatcher dispatcher)
    {
        _dispatcher = dispatcher;
    }

    public Task PostProcessAccountCreation(AccountCreated @event)
        => _dispatcher.PublishAsync(@event);
}
```

## Table of Contents

- [Joint](https://github.com/flapek/Joint)
- [Joint.Auth](https://github.com/flapek/Joint.Auth)
- [Joint.CQRS.Commands](https://github.com/flapek/Joint.CQRS.Commands)
- [Joint.CQRS.Events](https://github.com/flapek/Joint.CQRS.Events)
- [Joint.CQRS.Queries](https://github.com/flapek/Joint.CQRS.Queries)
- [Joint.DB.Mongo](https://github.com/flapek/Joint.DB.Mongo)
- [Joint.DB.Redis](https://github.com/flapek/Joint.DB.Redis)
- [Joint.Docs.Swagger](https://github.com/flapek/Joint.Docs.Swagger)
- [Joint.Exception](https://github.com/flapek/Joint.Exception)
- [Joint.Logging](https://github.com/flapek/Joint.Logging)
- [Joint.WebApi](https://github.com/flapek/Joint.WebApi)
