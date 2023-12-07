# Azure EventHub

# 1. Create an Azure Event Hub

We input the data for creating a new **Azure EventHub**:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/73e5e771-b63d-4cea-abe9-786cc46c331f)

We select the **Basic tier** for optimizing the cost:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/358c4036-fd46-44b0-aba2-f6084a288bd0)

We leave the other options with the default values:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/42c982bc-58cc-4b6b-8355-c608e8ce43c3)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/42c6da99-5565-40df-a222-b089994041f1)

Then we press the "**Create**" button:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/c51483d9-1a4e-4874-b7f9-122e067db790)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/8aa1372f-47e4-4a71-ae9a-a5b86dd665e3)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/beda6c87-08bd-4278-8be6-16beadc0359e)

Now we create an Event Hub

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/a6af56eb-476d-4403-b88f-b7472a9af8ac)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/4a4a14bf-67e2-4b45-9378-39ca3d8c6a4b)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/9a885d2c-2bbf-469f-8b4c-fe60ce5e42d8)

# 2. Create a C# console application (with .NET 8) in Visual Studio

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/dc12bdb8-c1b3-4f1d-8df2-42fa798fa3cd)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/2348157a-e2f6-44c7-9de6-64304813a852)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/05daad0a-8dec-4119-8f90-8c0cd6cab158)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/be31e079-d8ac-47eb-8238-7bb2107a2485)


# 3. Input the application source code for sending the messages to the Azure Event Hub

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading.Tasks;
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Producer;

string EventHubConnectionString = "Endpoint=sb://resourcegroupnamespace1.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=ojE7f1xAMJ2gpwK1zmsdwkQ4YdC65bhQR+AEhJX8PDM=";
string EventHubName = "myeventhub";

await SendEventsAsync(5); // Specify the number of events you want to send

async Task SendEventsAsync(int numberOfEvents)
{
    await using (var producerClient = new EventHubProducerClient(EventHubConnectionString, EventHubName))
    {
        List<EventData> eventBatch = new List<EventData>();

        for (int i = 0; i < numberOfEvents; i++)
        {
            try
            {
                string messageBody = $"Message {i}";
                var eventData = new EventData(Encoding.UTF8.GetBytes(messageBody));
                eventBatch.Add(eventData);
                Console.WriteLine($"Event added to batch: {messageBody}");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error creating event: {ex.Message}");
            }
        }

        try
        {
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine($"Batch of events sent successfully");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error sending batch of events: {ex.Message}");
        }
    }
}
```


