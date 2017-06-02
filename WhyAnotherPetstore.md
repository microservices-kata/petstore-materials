# Why Another Petstore ?

## What's petstore

[Petstore](https://en.wikipedia.org/wiki/Pet_store#In_computer_science), originally a Java EE demo made by Sun Inc. As it base on a very daily-life-style scenario, the business logic above its model won't be difficult for anyone to understand. so petstore have been used in many places as the first PoC application.

## Why petstore again

In this ms kata material, we'll like to setup a mini demo of MicroService Architecture, aim to clarify basic concept of ms: how it looks like, how it works, what's the benefit and common drawback, etc. And just as expected, we reused the petstore topic.

The coding part of this project won't be challenge at all: none of the service have complicate business logic nor hard algorithm. However, below are the things that you may need to focus on:

**1. Service Design**

Service design have never been an easy thing, especially when it come to the case of micro-serivces:  more flexiblity to stretch out and more possiblity to make mistake.

A explain of current service partition:

- *User Service*: should be as universal as possible, it may be used in other system in the same organization.
- *Order Service*: business related, but still can be somehow universal, may contains other kind of order beside pet.
- *Inventory Service*: business related, it keep the possible category of pet, pet shops and pets information.
- *Web BFF*: a composite service, provide as easy-to-use as possible APIs for web page.
- *API Gateway*: security and authentication checking service, all service behind this one is conside as internal.

**2. Where there is an invoke, there is a contract**

All service should provide APIs, alone with a concise API docs. More important thing is whoever using those APIs should also give its API contract file to the service provider.

An `API Contract` usually contains which APIs have been used and what kind of request/response format is expected. `Pact` is a practical standards of the cross programing language customer-driven-contract-test toolkit. It dynamically create contract file (pactfile) from code to check whether the service has made any destructive changes.

Usually pact verify should be setup as part of continuous delivery pipeline.

**3. MicroService & Distribution**

Distributed transaction and Eventual consistency. You know it : )

