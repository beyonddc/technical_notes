/*
 * Notes on Akka
 * http://akka.io/
 */
 
Program concurrency and scalability at a high level
-- It is flexible that it could be run pretty much on any device (e.g. smartphone, desktop, server and etc.)
-- Manage system overload
-- Automatic replication and distribution for fault-tolerance and scalability
-- Automatic and adaptive load balancing

What is an Actor?
-- Akka's unit of code organization is called an Actor
-- Actor helps you create concurrent, scalable and fault-tolerant applications
-- Like Java EE servlets and sessions beans, Actor is a model for organizing your code that keeps many 'policy decisions' separate from the business logic
-- Actor is the fundamental unit of computation that embodies:
---- Processing
---- Storage
---- Communcation
-- 3 axioms - When an Actor receives a message it can
---- Create new Actors
---- Send messages to Actor it knows
---- Designate how it should handle the next message it receives

Raise the abstraction level
-- Never think in terms of shared state, state visibility, threads, locks, concurrent collections, thread notification and etc.
-- Low level concurrency plubming becomes simple workflow (you only think about how message flow in the system).
-- You get high CPU utilization, low latency, high throughput and scalability (for free as part of the model)
-- Proven and suprioer model for detecting and recovering from errors

Distributable by Design
-- Actors are location transparent and distributable by design
-- Scale up and out for free as part of the model
-- You get the perfect fabric for the cloud
---- elastic and dynamic
---- fault-tolerant and self-healing
---- adaptive load-balancing, cluster rebalancing and actor migration
---- build extemely loosely coupled and dynamic systems that can change and adapt at runtime
 
What can I use Actors for?
-- In different scnearios, an Actor can be an alternative to:
---- a thread
---- an object instance or component
---- a callback or listener
---- a singleton or service
---- a Java EE Session Bean or Message-Driven Bean
---- an out-of-process serivce
---- a Finite State Machine (FSM)

4 core Actor operations
-- 0: Define
---- Define message
---- Define actor and how it process the message
-- 1: Create
---- Creates a new instance of an Actor
---- Extremely lightweight (2.7 million per Gb RAM)
---- Very strong encapsulation - encapsulates:
------ state
------ behavior
------ message queue
---- State and behavior is indistinguishable from each other
---- Only way to observe state is by sending an actor a message and see how it reacts
-- 2: Send
---- Sends a message to an Actor
---- Asynchronous and Non-blocking (fire and forget)
---- Everything happens Reactively (actor reactives to message, loosely couple)
------ An Actor is passive until a message is sent to it which triggers something within the Actor
------ Message is the Kinetic Energy in an Actor system
------ Actors can have lots of buffered Potential Energy but can't do anything with it until it is triggered by a message
------ Everything is asynchronous and lockless
------ Using Actor.tell() method to send message
------ To reply from an Actor to the sender, you invoke super.getSender().tell() method.
-- 3: Become
---- Redefines Actor's behavior
---- Triggered reactively by receive of message
---- In a type system analogy it is as if the object changed type - changed interface, protocol and implementation
---- Will now react differently to the message it receives
---- Behaviors are stacked and can be pushed and popped
------ Why would I want to do that?
-------- Let a highly contended Actor adaptively transform itself into an Actor Pool or a Router
-------- Implement an FSM (Finite State Machine)
-------- Implement graceful degradation
-------- Spawn up (empty) generic Worker processes that can become whatever the Master currently needs
-- 4: Supervise
---- Actors can supervise other Actors
---- Supervisor detects and responds to failures of the Actor(s) it supervises
---- This means that if An Actor crashes, a notification will be sent to its supervisor which can then decide what to do about it
---- This provides clean separation of processing and error handling

Router
-- A special case of an Actor
-- Router can be configured in a configuration file
-- Actor can be deploy on a remote machine via configuration file
