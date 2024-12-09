# mule-example-http-throttling

Example of HTTP Throttling

In some occasions, it is necessary to control the flow of messages to an external service or another Mule Application, depending on the use case this could be achievable through the use of proxy applications and policies such as [Rate limiting and Throttling](https://docs.mulesoft.com/api-manager/2.x/rate-limiting-and-throttling).
However, sometimes additional logic is required to customize the handling of the requests and routing of messages.
For those cases is possible to rely on a Mule Application to do it.

A simple pattern to handle this scenario is to use a Queue to store the requests received and to define a job that polls from that queue at the required interval to deliver the requests.

#### Example 1 - Using VM Queues

The examples attached to this article (For Mule Runtime versions 3.x and 4.x), consist of the following setup:

1.  The Mule configuration file `using-vm-queue.xml` contains:

-   The `using-vm-queue.queueRequests` flow basically simulates external invocations (Which could come from HTTP calls, for demonstration purposes a Poll scope/scheduler is set up in its place) and sends the messages to the VM queue.
-   The `using-vm-queue.jobExecutor` flow which at regular intervals retrieves messages from the queue and sends them one at a time.

2.  The Mule configuration file `using-vm-queue=sample-externalsystem` contains:

-   The flow `targetSystem` simulates an external system, basically receives the messages from the JobExecutor.

This implementation uses a VM queue however, it could be adapted to use different queue brokers (JMS, AMQP, ActiveMQ) depending on the scenario.

#### Example 2 - Using Size based aggregator

The flows at `using-size-based-aggregator.xml` discusses a similar implementation using the "Size based aggregator"

1.  The flow `using-size-based-aggregator.trigger` gets triggered every 30 seconds and pushes 10 messages to `using-size-based-aggregator.process-and-wait`
2.  The concurrency of the flow `using-size-based-aggregator.process-and-wait` is "1", this limits the parallel executions and throttles at a level.
3.  The "Size based aggregator" is configured to accumulate the records in batches of "2". This step again throttles the message processing at one more level.

This implementation uses the logic of [Size-Based Aggregator](https://docs.mulesoft.com/aggregators-module/1.0/aggregators-size-example) module in the implementation.

Both the examples discussed are different approaches to achieve the same functionality. There can be other design approaches with which the same functionality can be acheived.

Disclaimer: This solution provides a suggestion that should be considered in conjunction with your specific use-case and requirements and does not represent a complete solution for all circumstances.

https://help.salesforce.com/s/articleView?id=001114514&language=da&type=1
