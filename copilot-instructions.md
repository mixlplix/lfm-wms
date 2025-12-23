# GitHub Copilot Instruction Document
## Canonical API + Connector SDK + Azure Service Bus Integration

## Purpose
These instructions tell GitHub Copilot how to generate:

- The .NET solution  
- The project structure  
- The data model  
- The API controllers  
- The SDK  
- The sample connector  
- The Azure Service Bus integration
- The documentation
- The logging

---

# 1. Solution Requirements

Generate a .NET 8 solution with three projects:

1. **CanonicalApi** — ASP.NET Core Web API  
2. **ConnectorSdk** — class library  
3. **SampleWmsConnector** — console app or worker service  

Follow the architecture and conventions below.

---

# 2. Technologies

- .NET 8  
- ASP.NET Core Web API  
- Azure.Messaging.ServiceBus  
- System.Text.Json  
- Dependency Injection  
- Clean Architecture  
- Async/await everywhere  
- Swagger/OpenAPI  

---

# 3. Project Structure
/src /CanonicalApi /Controllers /Services /Messaging /Models /Configuration
/ConnectorSdk /Abstractions /Base /Models /Messaging
/SampleWmsConnector /Services /Messaging /Models


---

# 4. Canonical Data Model

Generate the following DTOs as `record` types:

### InventoryDto
- string Sku  
- string Location  
- int Quantity  

### ShipmentRequest
- string ShipmentId  
- string DestinationWmsId  
- List<ShipmentLine> Items  

### ShipmentLine
- string Sku  
- int Quantity  

### ShipmentResponse
- string ShipmentId  
- string Status  
- DateTime Timestamp  

### InventoryUpdateEvent
- string Sku  
- int Quantity  
- string Location  
- DateTime UpdatedAt  

---

# 5. Canonical API Requirements

### Controllers

#### InventoryController
- `GET /api/inventory`
- Calls `IInventoryService`

#### ShipmentsController
- `POST /api/shipments`
- Publishes `ShipmentCreated` event

#### HealthController
- `GET /api/health`

---

# 6. Services

### IShipmentService
- `Task CreateShipmentAsync(ShipmentRequest request)`

### IInventoryService
- `Task<IEnumerable<InventoryDto>> GetInventoryAsync()`

---

# 7. Azure Service Bus Integration

### Topics
- `shipments`
- `inventory-updates`
- `acknowledgements`

### Base Classes

#### ServiceBusPublisher<T>
- Publishes messages with:
  - JSON body  
  - MessageId  
  - Subject  

#### ServiceBusConsumer<T>
- Subscribes to a topic  
- Deserializes JSON  
- Calls `HandleMessageAsync(T message)`  

---

# 8. Connector SDK Requirements

### Interfaces
#### IWmsConnector
- `Task<ShipmentResponse> CreateShipmentAsync(ShipmentRequest request)`
- `Task<IEnumerable<InventoryDto>> GetInventoryAsync()`

### Utilities
- Retry policies (Polly)
- Error normalization

---

# 9. Sample WMS Connector Requirements

Implement:

- ShipmentConsumer  
- InventoryPublisher  
- MockWmsApiClient  

Connector must:

- Subscribe to `shipments`  
- Translate canonical → WMS API  
- Publish `ShipmentAcknowledged`  
- Publish `InventoryUpdated`  

---

# 10. Coding Style

- PascalCase for classes/methods  
- camelCase for locals  
- Use DI everywhere  
- Use `record` for DTOs  
- Use `CancellationToken`  

---

# 11. Copilot Generation Rules

When generating code:

1. Follow the project structure exactly  
2. Generate controllers → services → messaging layers  
3. Use DTOs exactly as defined  
4. Implement Service Bus publishers/consumers  
5. Use DI for all components  
6. Add XML documentation comments  
7. Generate unit tests where appropriate  

### 12. Documentation
- Generate README.md for each project
- Include setup instructions
- Document API endpoints with Swagger
- Make documentation MCP Server compliant
- Provide examples for SDK usage
- Document Azure Service Bus topics and message formats
- Include logging strategy and examples
- Document error handling and retry policies
- Provide architecture overview diagrams
- Include contribution guidelines
- List dependencies and versions used
- Provide contact information for support
- Include changelog for future updates
- Document deployment instructions for Azure Service Bus integration
- Provide examples of common use cases
- Include performance considerations and best practices
- Document security considerations and best practices
- Provide troubleshooting tips and FAQs
- Include code samples for key functionalities
- Document versioning strategy for API and SDK
- Provide guidelines for extending the SDK and connector
- Include licensing information
- Document testing strategy and coverage
- Provide links to additional resources and references
- Include a glossary of terms used in the project
- Document the build and release process
- Provide examples of configuration settings
- Include a roadmap for future enhancements
- Document integration points with other systems
- Provide examples of logging output and formats
- Document monitoring and alerting strategies
- Include a section on performance tuning and optimization
- Provide guidelines for code reviews and contributions
- Document the use of third-party libraries and tools
- Include a section on common pitfalls and how to avoid them
- Provide examples of error handling patterns
- Document the use of async/await and concurrency considerations
- Include a section on scalability and load balancing strategies
- Provide examples of deployment scenarios
- Document the use of configuration management tools
- Include a section on backup and disaster recovery strategies
- Provide guidelines for maintaining code quality and standards
- Document the use of logging frameworks and best practices
- Include a section on security best practices and compliance requirements
- Provide examples of API versioning strategies
- Document the use of caching strategies and best practices
- Include a section on data modeling and schema design principles
- 
