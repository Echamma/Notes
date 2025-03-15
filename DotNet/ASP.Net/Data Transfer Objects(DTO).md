# Data Transfer Object (DTO) Pattern

## What is it?
A **Data Transfer Object (DTO)** is a simple object used to transfer data between layers in an application, particularly in APIs. It acts as a container for data, ensuring only the necessary information is shared while excluding sensitive or irrelevant details.

## Why you should use it:
1. **Security:** Prevents exposing sensitive data (e.g., passwords, secret info) to the client.
2. **Efficiency:** Reduces payload size, improving performance by sending only required fields.
3. **Decoupling:** Keeps internal models separate from API responses, preventing changes in internal structures from breaking the API.
4. **Versioning:** Simplifies managing different API versions by controlling the data structure at the DTO level.
5. **Readability:** Makes the code cleaner by providing a clear contract for what data is sent to the client.

## How to implement:

### 1. Create the DTO:
* Add a new class (e.g., `GameCharacterResponse`) with only the properties you want to expose.

```csharp
public class GameCharacterResponse 
{
    public string Name { get; set; }
    public int Level { get; set; }
    public string Class { get; set; }
    public int Health { get; set; }
    public string Weapon { get; set; }
    public List<string> Inventory { get; set; }
}
```

### 2. Map Data Manually:
* In the controller, map the model to the DTO manually.

```csharp
[HttpGet("{id}")]
public GameCharacterResponse GetCharacter(int id)
{
    var character = database.GetCharacterById(id);
    var response = new GameCharacterResponse
    {
        Name = character.Name,
        Level = character.Level,
        Class = character.Class,
        Health = character.Health,
        Weapon = character.Weapon,
        Inventory = character.Inventory
    };
    return response;
}
```

### 3. Map Data Automatically (Optional):
* Use a mapping library like **Mapster** or **AutoMapper** to handle this automatically.
* Install Mapster with:

```shell
dotnet add package Mapster
```

* Then in the controller:

```csharp
[HttpGet("{id}")]
public GameCharacterResponse GetCharacter(int id)
{
    var character = database.GetCharacterById(id);
    var response = character.Adapt<GameCharacterResponse>();
    return response;
}
```

## Key Benefits Recap:
* Reduces exposure of sensitive data
* Improves performance with smaller payloads
* Keeps business logic and API responses separate
* Makes code cleaner and easier to maintain

## Common Use Cases:
* Web API endpoints returning data to clients
* Microservice communication
* Frontend-backend data exchange
* Third-party API integration

## Best Practices:
* Keep DTOs simple and focused on data transfer only (no business logic)
* Create separate DTOs for requests and responses when needed
* Use validation attributes on request DTOs to ensure data integrity
* Consider using inheritance or composition for related DTOs
* Name DTOs according to their purpose (e.g., `UserResponse`, `CreateUserRequest`)

## Advanced Techniques:

### DTO Projection with LINQ:
```csharp
public IEnumerable<GameCharacterResponse> GetAllCharacters()
{
    return database.Characters
        .Select(c => new GameCharacterResponse
        {
            Name = c.Name,
            Level = c.Level,
            Class = c.Class,
            Health = c.Health,
            Weapon = c.Weapon,
            Inventory = c.Inventory
        })
        .ToList();
}
```