# Amido Stacks Messaging AWS SNS

This library is wrapper around [Amazon Simple Notification Service](https://aws.amazon.com/sns/).
The main goals are:

1. To publish messages to a configured SNS topic.
2. To receive messages from a configured SNS topic.

## 1. Requirements

You will need an SNS instance in order to use this library. To create an SNS instance, you can follow the instructions in the [AWS SNS Documentation](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sns-apis-intro.html).

## 2. Registration/Usage

### 2.1 Dependencies

- `Amido.Stacks.Application.CQRS.Abstractions`
- `Amido.Stacks.Configuration`
- `Amido.Stacks.Core`
- `AWSSDK.SimpleNotificationService`

### 2.2 Currently Supported messages

The library currently supports:

- Publishing and receiving events that implement `Amido.Stacks.Application.CQRS.ApplicationEvents.IApplicationEvent`.

### 2.3 Usage in dotnet application

#### 2.3.1 Event

In this example the `MenuCreatedEvent` is handled by the `MenuCreatedEventHandler`.  The event must implement the `IApplicationEvent` interface and the handler must implement the `IApplicationEventHandler<NotifyEvent>` interface.  Both of these interfaces are found in the [Amido.Stacks.Application.CQRS.ApplicationEvents namespace](https://github.com/Ensono/stacks-dotnet-packages-cqrs-abstractions).

***MenuCreated.cs***

```cs
public class MenuCreatedEvent : IApplicationEvent
{
    public MenuCreatedEvent(Guid correlationId, int eventCode, int operationCode)
    {
        CorrelationId = correlationId;
        EventCode = eventCode;
        OperationCode = operationCode;
    }

    public Guid CorrelationId { get; }
    public int EventCode { get; }
    public int OperationCode { get; }
}
```

***MenuCreatedHandler.cs***

```cs
public class MenuCreatedEventHandler : IApplicationEventHandler<MenuCreatedEvent>
{
    public Task HandleAsync(MenuCreatedEvent applicationEvent)
    {
       //  Code to handle the event...
        return Task.CompletedTask;
    }
}
```

#### 2.3.1.1 AWS Options Configuration

***appsettings.json***

```json
{
  "AwsSnsConfiguration": {
      "TopicArn": {
        "Identifier": "TOPIC_ARN",
        "Source": "Environment"
      }
  }
}
```

***Usage***

```cs
public class Startup
{
    public void ConfigureServices(IServiceCollection services, WebHostBuilderContext context)
    {
        services.Configure<AwsSnsConfiguration>(context.Configuration.GetSection("AwsSnsConfiguration"));
        services.AddAwsSns(context.Configuration);
         services.AddSecrets();  // To read the secret defined as an environment variable appsettings.json.
    }
}
```
