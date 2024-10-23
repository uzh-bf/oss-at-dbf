---
title: Azure Functions
---

# What are Azure Functions?

> Azure Functions is a serverless compute service provided by Microsoft Azure that allows developers to run event-triggered code without the need to provision or manage infrastructure [1](https://www.serverless360.com/azure-functions). This service is designed to execute a piece of code or script in response to a variety of events, making it ideal for tasks that need to be performed quickly, often within seconds or less [2](https://www.azureguru.org/some-key-features-of-azure-functions/).
>
> Azure Functions supports multiple programming languages, including C#, F#, JavaScript, Java, PowerShell, Python, and TypeScript, among others [1](https://www.serverless360.com/azure-functions). The service is designed to be scalable, automatically allocating necessary resources when the demand for code execution increases, and disposing of any leftover resources when the demand decreases [3](https://revdebug.com/blog/azure-functions-overview-use-cases/).
>
> Azure Functions can be triggered by a variety of events, such as HTTP requests, timers, or messages from other Azure services [1](https://www.serverless360.com/azure-functions). They are commonly used for tasks like reminders and notifications, scheduled tasks, file processing, and data stream processing [3](https://revdebug.com/blog/azure-functions-overview-use-cases/). They can also be used to build web APIs, respond to database changes, process IoT streams, manage message queues, and more [9](https://learn.microsoft.com/en-us/azure/azure-functions/).
>
> Azure Functions offers several hosting options, including the Consumption plan where you only pay for execution time, and the Premium plan which provides always warm instances for faster response times [4](https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview). The service integrates with various Azure services and offers a high level of hardware abstraction, allowing developers to focus on core business logic [7](https://azure.microsoft.com/en-us/products/functions).
>
> Source: perplexity.ai with the prompt "what are azure functions?"

# Examples

- KlickerUZH: Azure Function with an HTTP trigger that adds a message to [[azure-service-bus]] (queue) for asynchronous further processing (in [[typescript]]) -> https://github.com/uzh-bf/klicker-uzh/tree/v3/apps/func-incoming-responses
- KlickerUZH: Azure Function with an [[azure-service-bus]] trigger that processes the message and updates state in a Redis cache (in [[typescript]]) -> https://github.com/uzh-bf/klicker-uzh/tree/v3/apps/func-response-processor
- KlickerUZH: Azure Function with an [[azure-service-bus]] trigger that reads from MongoDB and stores a new file on [[azure-blob-storage]] (in [[typescript]]) -> https://github.com/uzh-bf/klicker-uzh/tree/v3/apps/func-migration-v2-export
- KlickerUZH: Azure Function with an [[azure-blob-storage]] trigger that processes a JSON blob and updates data in PostgreSQL (in [[typescript]]) -> https://github.com/uzh-bf/klicker-uzh/tree/v3/apps/func-migration-v3-import
- KlickerUZH: [[github-actions]] workflow for automated deployment of an Azure Function -> https://github.com/uzh-bf/klicker-uzh/blob/v3/.github/workflows/v3_klickeruzhprod-responses.yml

# Further Resources

- Azure Functions Documentation: https://learn.microsoft.com/en-us/azure/azure-functions/
- Azure Functions Best Practice: https://learn.microsoft.com/en-us/azure/azure-functions/functions-best-practices
- Integrating Azure Functions with Container Apps: https://learn.microsoft.com/en-us/azure/azure-functions/functions-deploy-container-apps?tabs=acr%2Cbash&pivots=programming-language-typescript
- Github Actions for Azure Functions Deployment: https://learn.microsoft.com/en-us/azure/azure-functions/functions-how-to-github-actions?tabs=windows%252Cdotnet
