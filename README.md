# How to create a C# console application, with .NET 8, to send/read messages to/from your Azure EventHub

For further information about EventHubs, see also these links:

https://learn.microsoft.com/en-us/azure/messaging-services/

https://learn.microsoft.com/en-us/azure/event-hubs/

https://learn.microsoft.com/en-us/training/modules/azure-event-hubs/

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

# 2. Create a C# console application (with .NET 8) in Visual Studio 2022 Community Edition

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/dc12bdb8-c1b3-4f1d-8df2-42fa798fa3cd)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/2348157a-e2f6-44c7-9de6-64304813a852)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/05daad0a-8dec-4119-8f90-8c0cd6cab158)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/be31e079-d8ac-47eb-8238-7bb2107a2485)

Load the Azure EventHub library with Nuget:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/355c3988-776e-44eb-a0e2-a691327b4971)

# 3. Copy the EventHub Connection String and EventHub Name from Azure Portal

We first copy the EventHub connection string:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/9fb020a7-238f-4808-8dbd-865768245aaf)

Then we copy the EventHub name:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/03e2cd33-46e4-4e3c-8a50-05728624ac86)

# 4. Input the application source code for "Sending" and "Receiving" messages to/from the Azure Event Hub

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Threading.Tasks;
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Consumer;
using Azure.Messaging.EventHubs.Producer;

string EventHubConnectionString = "Endpoint=sb://resourcegroupnamespace1.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=ojE7f1xAMJ2gpwK1zmsdwkQ4YdC65bhQR+AEhJX8PDM=";
string EventHubName = "myeventhub";

string ConsumerGroup = "$Default";

await SendEventsAsync(5); // Specify the number of events you want to send
await ReceiveEventsAsync();

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

async Task ReceiveEventsAsync()
{
    await using (var consumerClient = new EventHubConsumerClient(ConsumerGroup, EventHubConnectionString, EventHubName))
    {
        Console.WriteLine("Listening for events...");

        try
        {
            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync())
            {
                string messageBody = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
                Console.WriteLine($"Received event: {messageBody}");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error reading events: {ex.Message}");
        }
    }
}
```


# 5. Build and run the application in Visual Studio 2022 Community Edition

This is the ouput we get after running the application:

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/e1f3f4d5-1ddc-42d3-8065-cea0fe1c86b4)


# 6. Check in Azure portal we received the messages in the EventHub

Open Azure portal and navigate to the **Monitor** service. 

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/c2912ec6-503c-4f48-8dc2-4d5f222d8cf9)

Then select **Metrics** and the EventHub we would like to monitor

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/8abba4d7-f50e-462c-81a5-1a21d0854856)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/fcb93c52-bd38-42cd-9902-d39e6066789b)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/7d094d23-d112-4c57-9638-2eae097d69d0)

![image](https://github.com/luiscoco/Azure_EventHub/assets/32194879/fbfdbac8-f35f-470f-a4b1-a38c32c243ee)


