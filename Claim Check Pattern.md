> **Claim Check Pattern** This pattern is also known as **Reference-Based Messaging**, and was originally described in the book Enterprise Integration Patterns, by Gregor Hohpe and Bobby Woolf.

![image](https://user-images.githubusercontent.com/1255516/129319927-075f66af-0d3e-48aa-9aa5-a2b87acad69d.png)


**Purpose and Usage:** 
Split a large message into a claim check and a payload. Send the claim check to the messaging platform and store the payload to an external service. This pattern allows large messages to be processed, while protecting the message bus and the client from being overwhelmed or slowed down. This pattern also helps to reduce costs, as storage is usually cheaper than resource units used by the messaging platform.
- This pattern should be used whenever a message cannot fit the supported message limit.
- The pattern can also be used if the payload should be accessed only by services that are authorized to see it.

**Background:**
Sending large message payloads (such as, BLOB types -  images, sound files, text doduments, binary data, etc.)  to the message bus directly is not recommended, because they require more resources and bandwidth to be consumed. Large message payloads can also slow down the entire solution, because messaging platforms are usually fine-tuned to handle huge quantities of small messages. Also, most messaging platforms have limits on message size, so user may need to work around these limits for large messages.

**Solutions:**
Store the entire message payload into an external service, such as a data store or database. Get the reference to the stored payload, and send just that reference to the message bus. The reference acts like a claim check used to retrieve a piece of luggage, hence the name of the pattern. Clients interested in processing that specific message can use the obtained reference to retrieve the payload, if needed. 

**Points to Remember:**
Be mindful for following points before leveraging this pattern:
1. Consider deleting the message data after consuming it, if you don't need to **archive** the messages. Although blob storage is relatively cheap, it costs some money in the long run, especially if there is a lot of data. Deleting the message can be done **synchronously** by the application that receives and processes the message, or asynchronously by a separate dedicated process. The asynchronous approach removes old data with no impact on the throughput and message processing performance of the receiving application.

2. Storing and retrieving the message causes some additional overhead and latency. You may want to **implement logic** in the sending application to use this pattern only when the message size exceeds the data limit of the message bus. The pattern would be skipped for smaller messages. This approach would result in a conditional claim-check pattern.

**Approach of Implementation:**
1. **Automatic claim-check generation**. This approach uses AWS EventBridge, SQS and SNS services, to automatically generate the claim check and push it into the message bus.

2. **Manual claim-check generation**. In this approach, the sender is responsible for managing the payload. The sender stores the payload using the appropriate service, gets or generates the claim check, and sends the claim check to the message bus. E.g., Kafka client writes the payload.


**Alternative Option of this pattern:**
An alternative pattern for handling large messages is **[Split](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Sequencer.html "Split")** and **[Aggregate](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Aggregator.html "Aggregate")**.
