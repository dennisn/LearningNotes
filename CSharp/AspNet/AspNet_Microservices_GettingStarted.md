# ASP.NET Core Microservices: Getting started

## 1. Introduction
  - From "ASP.NET Core 3 Microservices: Getting Started" by Roland Guijt
  - About why microservices, creating one and how microservices communicate to each other

## 2. Create a Microservice
  - Using VS templates

## 3. Conneting Microservices Syn/Async
  - Using service bus (e.g. Kafka, RabbitMQ, Azure service bus) to de-coupling caller/receiver

## 4. Microservices considerations and Design
  - Main reason: divide into subprojects --> scalability, smaller team/code-based
  - Main problems: 
    + Increase complexity as a whole --> need to manage inter-action between each sub-parts, both in development, deployment & monitoring
    + Eventual consistency: maybe a problem for specific business

