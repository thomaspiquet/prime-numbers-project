# Distributed Prime Numbers Finder

## Goal of this project

```
The purpose of this project is to practice certain methodologies of software craft,
such as Test-Driven Development (TDD) and clean code principles.

It will also be used to implement technologies like RabbitMQ and Server-Sent Events (SSE),
and to learn some basics of Amazon Web Services (AWS).
```

## Initial Needs ("client" view)

```
I need a website that allows users to select a range of numbers and then return the list of prime numbers
within that range. 

The website should show to the user the progress of the task, returning prime numbers as soon as they are found,
without waiting for the entire range to be processed. 

If the website experiences high traffic, it should inform the user of this, as well as their position in the
queue if there's a delay.

The monthly cost for maintaining the website's infrastructure should not exceed $10.
```

## Technical view

```
The website will be developed using React and will interact with a NestJS-based API.

This API will handle large range of numbers by dividing them into chunks. Each chunk will be send to a RabbitMQ server.
NestJS Serverless functions will consume these messages to discover prime numbers.

Each function, after processing a range, will store the results in a Redis cache database.

When a batch of prime numbers is retrieved by the API, it will be immediately sent to the client 
using Server-Sent Events (SSE).

Since discovering new prime numbers is computation-intensive, to avoid exceeding the budget,
the system will first check in Redis if the chunk has already been processed.

To reduce costs, a cooldown period will be implemented for the prime discovery API calls.

Additionally, if RabbitMQ accumulates more than a specified number of pending messages,
the API will inform users of their position in the queue via SSE,
considering the concurrent limits set for serverless functions.
```

## Architecture 

![Architecture: AWS Oriented](assets/prime_numbers_arch.png?raw=true)

Client on GitHub Pages
```
Free hosting for small front app.
```

API on Amazon EC2
```
As API will not process by itself the discovery of prime numbers, it will be acceptable to only have a single VM.
It's just an exercice, not a company website.
```

Message Broker on Amazon MQ
```
Nothing special to say here, the project need a message broker in order to have a distributed prime numbers finder.
```

Prime Numbers Finder on AWS Lambda
```
Prime numbers discovery can be an heavy process, it can't be supplied by the API.
Distributed process over Lambdas allow a fast and non blocking discovery of prime numbers. 
```

Cache on Amazon Elasticache
```
Elasticache will store the already processed numbers range from Lambda.
The API will be able to take a look at it before launching Lambda for a specific range.
The objective is to reduce the call of Lambda.
```

### Expected Costs

```
Completely Free
    - GitHub pages
   
12 months free for 1 instance
    - Amazon EC2
    - Amazon MQ
    - Amazon Elasticache
    
Free with limitations
    - AWS Lambda (1 000 000 requests and 3 000 000 seconds / month)
```

Here, the Lambdas will be the only part that can overdue the budget.

The target cost is 10 $ a month. After that, the finding of newer prime numbers will be stopped. 
Only the already discovered primes stored in cache will be returned.

## Project links

[Front repo](https://github.com/thomaspiquet/prime-numbers-front)

[API repo](https://github.com/thomaspiquet/prime-numbers-api)

[Finder repo](https://github.com/thomaspiquet/prime-numbers-finder)

## Author

Thomas Piquet