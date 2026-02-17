---
name: zeebe-workflow-patterns
description: Camunda/Zeebe workflow orchestration patterns for .NET/C# projects
license: MIT
compatibility: ["*.cs", "*.bpmn", "*.csproj"]
metadata:
  category: workflow
  tags: [zeebe, camunda, workflow, orchestration, bpmn, dotnet, csharp]
  version: 1.0.0
---

# Zeebe Workflow Patterns for .NET/C#

Practical patterns and best practices for implementing Camunda/Zeebe workflow orchestration in C# projects.

## 1. Zeebe Job Worker Pattern

### Worker Registration and Configuration

```csharp
// Worker registration with typed handler
services.AddZeebeWorker()
    .AddWorker<ProcessPaymentWorker>()
    .Configure(options =>
    {
        options.MaxJobsActive = 5;
        options.PollingTimeout = TimeSpan.FromSeconds(30);
        options.Timeout = TimeSpan.FromMinutes(5);
        options.PollInterval = TimeSpan.FromMilliseconds(100);
        options.RetryBackoff = TimeSpan.FromSeconds(3);
    });
```

### Job Handler Implementation

```csharp
[JobType("process-payment")]
public class ProcessPaymentWorker : IJobHandler
{
    private readonly IPaymentService _paymentService;
    private readonly ILogger<ProcessPaymentWorker> _logger;

    public async Task HandleJob(IJobClient client, IJob job)
    {
        try
        {
            // Extract input variables
            var orderId = job.Variables["orderId"]?.ToString();
            var amount = decimal.Parse(job.Variables["amount"]?.ToString() ?? "0");
            
            _logger.LogInformation("Processing payment for order {OrderId}", orderId);
            
            // Execute business logic
            var transactionId = await _paymentService.ProcessPayment(orderId, amount);
            
            // Complete job with output variables
            await client.NewCompleteJobCommand(job)
                .Variables(new { transactionId, status = "completed" })
                .Send();
        }
        catch (PaymentDeclinedException ex)
        {
            // Throw BPMN error (caught by error boundary event)
            await client.NewThrowErrorCommand(job)
                .ErrorCode("PAYMENT_DECLINED")
                .ErrorMessage(ex.Message)
                .Send();
        }
        catch (Exception ex)
        {
            // Fail job for retry (technical error)
            _logger.LogError(ex, "Failed to process payment");
            await client.NewFailCommand(job)
                .Retries(job.Retries - 1)
                .ErrorMessage(ex.Message)
                .Send();
        }
    }
}
```

### Key Points

- **Complete Job**: Signal success with `NewCompleteJobCommand()`
- **Fail Job**: Trigger retry with `NewFailCommand()` (decrements retry count)
- **Throw Error**: Trigger BPMN error boundary event with `NewThrowErrorCommand()`
- **Variables**: Keep payloads small (<4MB), use external storage for large data

## 2. Message Correlation

### Publishing Messages

```csharp
await zeebeClient.NewPublishMessageCommand()
    .MessageName("OrderShipped")
    .CorrelationKey(orderId) // Must match process instance correlation key
    .Variables(new { trackingNumber, carrier })
    .TimeToLive(TimeSpan.FromHours(24)) // Message expires if not correlated
    .Send();
```

### Correlation Key Design

**Good Keys** (unique, stable, business-meaningful):
- Order ID, Invoice Number, Customer ID
- Composite keys: `$"{customerId}_{orderId}"`

**Bad Keys** (non-unique, unstable):
- Timestamps, random GUIDs generated per message
- User session IDs that change

### Common Pitfalls

- **Mismatched Keys**: Process expects `orderId`, message sends `order_id`
- **Missing Messages**: TTL too short, message expires before correlation
- **Duplicate Correlation**: Same message correlates to multiple instances

## 3. Error Handling Patterns

### Retry Policies

Configure in BPMN or programmatically:

```xml
<!-- BPMN retry configuration -->
<zeebe:taskDefinition type="process-payment" retries="3" />
```

**Backoff Strategy**: Use exponential backoff for transient failures
- Retry 1: 3 seconds
- Retry 2: 9 seconds  
- Retry 3: 27 seconds

### BPMN Error vs Technical Error

| Type | When to Use | Outcome |
|------|-------------|---------|
| **BPMN Error** | Business exception (payment declined, item out of stock) | Triggers error boundary event, no retry |
| **Technical Error** (Fail Job) | Transient failure (network timeout, DB deadlock) | Retry with backoff |

### Incident Handling

Incidents occur when:
- Job retries exhausted (retries = 0)
- Job timeout exceeded without completion
- Expression evaluation fails

**Resolution**: Fix root cause, then retry incident via Operate UI or API

## 4. Testing Zeebe Workflows

### Unit Testing Workers

```csharp
[Fact]
public async Task HandleJob_PaymentSucceeds_CompletesJob()
{
    var mockClient = new Mock<IJobClient>();
    var mockJob = CreateMockJob(new { orderId = "123", amount = 100 });
    
    await _worker.HandleJob(mockClient.Object, mockJob);
    
    mockClient.Verify(c => c.NewCompleteJobCommand(mockJob)
        .Variables(It.Is<object>(v => HasTransactionId(v)))
        .Send(), Times.Once);
}
```

### Integration Testing

```csharp
// Use Zeebe test container
var container = new ZeebeContainer();
await container.StartAsync();

var client = container.CreateClient();
await client.NewDeployCommand()
    .AddResourceFile("order-process.bpmn")
    .Send();

var instance = await client.NewCreateInstanceCommand()
    .BpmnProcessId("order-process")
    .LatestVersion()
    .Variables(new { orderId = "123" })
    .Send();

// Assert process state
var processInstance = await GetProcessInstance(instance.ProcessInstanceKey);
Assert.Equal("payment-completed", processInstance.CurrentActivity);
```

## 5. Common Pitfalls

### Forgetting to Complete/Fail Jobs
**Problem**: Worker crashes or returns without completing → job times out → incident
**Solution**: Always complete, fail, or throw error in `finally` block or error handler

### Infinite Retry Loops
**Problem**: Job fails forever without resolution
**Solution**: Set max retries, implement circuit breaker, monitor incidents

### Large Variable Payloads
**Problem**: Variables >4MB cause errors, slow performance
**Solution**: Store large data externally (S3, blob storage), pass reference ID

### Missing Correlation Keys
**Problem**: Message published but process variable not set
**Solution**: Ensure correlation key variable set before message catch event

### Long-Running Jobs Without Heartbeat
**Problem**: Job times out while still processing
**Solution**: Send periodic heartbeats for jobs >5 minutes

## 6. BPMN Best Practices

### Process Design
- **Keep Simple**: 5-15 tasks per process, use sub-processes for complexity
- **Clear Naming**: Use verb + noun (e.g., "Process Payment", "Validate Order")
- **Error Boundaries**: Add error events for expected failures

### Versioning
- **Deploy New Versions**: Don't modify deployed processes
- **Migration**: Running instances continue on old version unless migrated
- **Deprecation**: Monitor old versions, migrate critical instances

### Performance
- **Worker Concurrency**: Tune `MaxJobsActive` based on throughput needs
- **Poll Interval**: Balance latency vs server load (100ms typical)
- **Variable Scope**: Use local variables when possible to reduce payload size

---

## Quick Reference

```csharp
// Complete job successfully
await client.NewCompleteJobCommand(job).Variables(output).Send();

// Fail job for retry
await client.NewFailCommand(job).Retries(job.Retries - 1).Send();

// Throw BPMN error
await client.NewThrowErrorCommand(job).ErrorCode("CODE").Send();

// Publish message
await client.NewPublishMessageCommand()
    .MessageName("name").CorrelationKey(key).Send();
```
