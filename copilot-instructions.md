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

