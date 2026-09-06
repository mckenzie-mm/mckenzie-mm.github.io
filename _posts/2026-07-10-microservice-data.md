---
layout: post
author: Mark
title: "Database Patterns in Microservices"
summary: Two different, commonly used patterns in Microservice.
tags: [kubernetes, docker, devops]
---
<i>This is copied from the AWS documentation, 'Patterns for enabling data persistence', with an example in Kubernetes from Anton Putra (github.com/antonputra)</i>
<br>
<br>
<hr>

## Database Per Service 
<b>Reproduced from AWS documentation: </b>
<i>"Loose coupling is the core characteristic of a microservices architecture, because each individual microservice can independently store and retrieve information from its own data store. By deploying the database-per-service pattern, you choose the most appropriate data stores (for example, relational or non-relational databases) for your application and business requirements. This means that microservices don't share a data layer, changes to a microservice's individual database do not impact other microservices, individual data stores cannot be directly accessed by other microservices, and persistent data is accessed only by APIs. Decoupling data stores also improves the resiliency of your overall application, and ensures that a single database can't be a single point of failure.</i>

<i>In the following illustration, different AWS databases are used by the 'Sales,' 'Customer,' and 'Compliance' microservices. These microservices are deployed as AWS Lambda functions and accessed through an Amazon API Gateway API. AWS Identity and Access Management (IAM) policies ensure that data is kept private and not shared among the microservices. Each microservice uses a database type that meets its individual requirements; for example, 'Sales' uses Amazon Aurora, 'Customer' uses Amazon DynamoDB, and 'Compliance' uses Amazon Relational Database Service (Amazon RDS) for SQL Server."</i>

![Alternative text for accessibility]({% link assets/images/database-per-service.png %})
**Figure 1. Database per service pattern.**

## Database shared among Services
<b>Reproduced from AWS documentation: </b>
<i>"In the shared-database-per-service pattern, the same database is shared by several microservices. You need to carefully assess the application architecture before adopting this pattern, and make sure that you avoid hot tables (single tables that multiple microservices write to). All your database changes must also be backward-compatible; for example, developers can drop columns or tables only if objects are not referenced by the current and previous versions of all microservices.</i>

<i>In the following illustration, an insurance database is shared by all the microservices and an IAM policy provides access to the database. This creates development time coupling; for example, a change in the "Sales" microservice needs to coordinate schema changes with the "Customer" microservice. This pattern does not reduce dependencies between development teams, and introduces runtime coupling because all microservices share the same database. For example, long-running "Sales" transactions can lock the "Customer" table and this blocks the "Customer" transactions."</i>


![Alternative text for accessibility]({% link assets/images/database-shared.png %})
**Figure 2. Shared Database pattern.**

![Alternative text for accessibility]({% link assets/images/database-kubernetes.png %})
**Figure 3. Shared Database pattern in Kubernetes. (Anton Putra)**






