# Microservice: how to do it right

## 1. The basics

Create a microservices system is never be an easy thing. There are several basic prerequisites accourding to Martin Fowler's blog [Prerequisites of Microservice](https://www.martinfowler.com/bliki/MicroservicePrerequisites.html):

![sketch](https://www.martinfowler.com/bliki/images/microservicePrerequisites/sketch.png)

#### 1.1 Rapid provisioning
You should be able to fire up a new server in a matter of hours. To be able to do such rapid provisioning, you'll need a lot of `automation`.

#### 1.2 Basic Monitoring
With many loosely-coupled services collaborating in production, things are bound to go wrong in ways that are difficult to detect in test environments. As a result it's essential that a `monitoring regime` is in place to detect serious problems quickly.

#### 1.3 Rapid application deployment
With many services to manage, you need to be able to quickly deploy them, both to test environments and to production. Usually this will involve a `deployment pipeline` that can execute in no more than a couple of hours.

#### 1.4 Devops culture
This may be the most hard but import clause. The pre-prerequisite
: `close collaboration` between developers and operations. It ensure that provisioning and deployment can be done rapidly, and you can react quickly when your monitoring indicates a problem. In particular any incident management needs to involve the development team and operations.


## 2. More tips

Devil is in the detail, when designing a microservice application, there could have traps. Below are some experience during our this years of microservice development. They may or may not fit your situation, just keep an eye on they.

#### 2.1 Database

We do prefer nosql database (e.g. mongodb) for a microservice application, since there's usually no real transaction within a small service, but the data schema may have constantly change due to the business model altering during its whole life cycle.

Besides, nosql is better for moduling, specially when your using the DDD (Domain-Driven Design) practice.

#### 2.2 Final consistency

Microservices is always a distribution system, distribution transaction is hard, get ready for a final consistency.

Restful is ok within a datacenter, but you should consider retry for each invoke, even all service are behind a load balance, fail may still happens. If a remote call failed permanently (after retries), a circuit-breaker or alert should be triggered, and roll back the local change if needed. If you can't roll back the local modification any more, write the issue into database or logfile as detail as possible, then do a manual fix-up later.

Use mq for remote calls cross datacenters, it prone to be final consistency. It would be even better if there is a consumed confirm feedback, so that you can know it when a remote-end fails and do the final-final consistency: manual fix-up.

#### 2.3 Asynchronous and timeout

For service provider side, better to use asynchronous api, especially when an operation is prone to be slow.

For service consumer side, always set a timeout when call http service, you won't know when the remote may hang on. Never let a small fail avalanche or let a slow request stop the world.

#### 2.4 Data ownership

Data can have several copies among the services, but it should have a ownership. That is to say, one inconsistency happened, only one source call the shots, all others are consider as cache.

