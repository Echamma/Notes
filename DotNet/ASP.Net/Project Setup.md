# .NET Clean Architecture Overview

## 1. Domain Layer (Core)

The **Domain Layer** represents the core of the application. It contains enterprise logic and types, and it’s completely isolated from other layers. It only holds the fundamental business rules.

**Responsibilities:**
- Define the core business logic.
- Contain domain entities, value objects, and aggregates.
- Handle domain events and exceptions.
- Define interfaces for persistence and other services.

**Folder Structure:**
/Domain 
├── Entities 
├── ValueObjects 
├── Aggregates 
├── Events 
├── Exceptions 
├── Interfaces

**What Each Folder Does:**

- **Entities:** Represent core business objects.  
  - Example: `Order`, `Customer`, `Product`.
- **ValueObjects:** Immutable objects representing domain concepts.  
  - Example: `Money`, `Address`.
- **Aggregates:** Groups of related entities treated as a single unit.  
  - Example: `Order` (root) with `OrderItems`.
- **Events:** Domain events signaling actions that occurred in the domain.  
  - Example: `OrderPlacedEvent`.
- **Exceptions:** Custom exceptions for handling domain-specific errors.  
  - Example: `InvalidOrderStateException`.
- **Interfaces:** Contracts for repositories, services, and external dependencies.  
  - Example: `IOrderRepository`, `IPaymentService`.

**Communication:**  
- No direct dependencies on other layers.  
- Exposes only interfaces and entities to the Application Layer.

---

## 2. Application Layer (Core)

The **Application Layer** contains use case implementations and application logic. It orchestrates tasks, leverages domain logic, and calls infrastructure services through abstractions.

**Responsibilities:**
- Implement use cases (application services).
- Handle queries and commands (CQRS pattern).
- Coordinate transactions and domain events.
- Use dependency injection to work with infrastructure implementations.

**Folder Structure:**
/Application 
├── UseCases 
├── Commands 
│ └── Queries 
├── Interfaces 
├── Exceptions 
├── DTOs 
├── Validators 
├── Mappings

**What Each Folder Does:**

- **UseCases:** Implements the core behaviors of the application.  
  - **Commands:** Handle actions that modify state.  
    - Example: `CreateOrderCommand`.  
  - **Queries:** Handle fetching data.  
    - Example: `GetOrderByIdQuery`.  
- **Interfaces:** Contracts for external services or repositories.  
  - Example: `IOrderService`.  
- **Exceptions:** Handle application-specific errors.  
  - Example: `OrderNotFoundException`.  
- **DTOs (Data Transfer Objects):** Simplifies communication between layers.  
  - Example: `CreateOrderRequestDto`, `OrderResponseDto`.  
- **Validators:** Validate incoming data, often with libraries like FluentValidation.  
  - Example: `CreateOrderValidator`.  
- **Mappings:** Map between domain models and DTOs, often using AutoMapper.  
  - Example: `OrderProfile`.

**Communication:**  
- Depends only on the Domain Layer.
- Exposes use cases for the API Layer to consume.
- Interacts with the Infrastructure Layer through abstractions.

---

## 3. Infrastructure Layer

The **Infrastructure Layer** handles external concerns and provides implementations of interfaces defined in the Domain and Application layers.

**Responsibilities:**
- Implement repositories and data access (e.g., EF Core, Dapper).
- Integrate with external APIs, messaging systems, and file systems.
- Handle authentication, authorization, and logging.

**Folder Structure:**
/Infrastructure 
├── Persistence 
├── Configurations  
├── Repositories 
├── Services 
├── Logging 
├── Identity

**What Each Folder Does:**

- **Persistence:** Manages data access and database configuration.  
  - **Configurations:** Entity mappings and database configuration.  
  - **Repositories:** Implements repository interfaces for data access.  
    - Example: `OrderRepository : IOrderRepository`.  
- **Services:** Implements external service integrations.  
  - Example: `StripePaymentService`.  
- **Logging:** Centralized logging using libraries like Serilog or NLog.  
- **Identity:** Handles authentication and authorization.  

**Communication:**  
- Depends on the Domain and Application Layers.
- Provides implementations of abstractions to the Application Layer via Dependency Injection.

---

## 4. API Layer (Presentation)

The **API Layer** is responsible for exposing the application to the outside world. It acts as a communication bridge between clients and the application logic.

**Responsibilities:**
- Define controllers, endpoints, and request/response handling.
- Handle input validation and output formatting.
- Implement middleware for cross-cutting concerns like error handling and authentication.

**Folder Structure:**
/API 
├── Controllers 
├── Middleware 
├── Filters 
├── Models (Request/Response) 
├── DependencyInjection


- **Controllers:** Define endpoints to handle incoming HTTP requests.  
  - Example: `OrderController`.  
- **Middleware:** Handles cross-cutting concerns like error handling.  
  - Example: `ExceptionHandlingMiddleware`.  
- **Filters:** Inject logic like authorization and validation into the request pipeline.  
  - Example: `ValidationFilter`.  
- **Models:** Contains request and response models specific to the API.  
  - Example: `CreateOrderRequest`, `OrderResponse`.  
- **DependencyInjection:** Configures DI for services, repositories, and other dependencies.  
  - Example: `ServiceCollectionExtensions`.  


**Communication:**  
- Depends on the Application Layer to invoke use cases.
- No direct communication with the Infrastructure Layer — everything goes through the Application Layer.

---

## Layer Dependencies Overview

- **Domain Layer**: No dependencies.
- **Application Layer**: Depends on the Domain Layer.
- **Infrastructure Layer**: Depends on both the Application and Domain Layers.
- **API Layer**: Depends only on the Application Layer.

---

## Additional Recommendations:

- Use Dependency Injection to decouple layers and inject implementations.
- Implement MediatR for CQRS and a clean separation between requests and handlers.
- Apply FluentValidation for request validation.


## Dependency Injection (DI)

**What is it?**  
Dependency Injection is a design pattern used to achieve Inversion of Control (IoC), making the code more modular and testable by injecting dependencies into classes instead of hard-coding them.

**Why use it?**
- Reduces coupling between classes.
- Makes unit testing easier by injecting mocks or fakes.
- Centralizes the configuration of dependencies.

**How to implement:**
1. Register services in the `Startup.cs` or `Program.cs` (for .NET 6+).
2. Use constructor injection to inject dependencies.

**Example:**
```csharp
// Program.cs
builder.Services.AddScoped<IOrderRepository, OrderRepository>();

// OrderService.cs
public class OrderService
{
    private readonly IOrderRepository _orderRepository;
    
    public OrderService(IOrderRepository orderRepository)
    {
        _orderRepository = orderRepository;
    }
}
```

