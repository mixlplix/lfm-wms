# Canonical API + Connector SDK + WMS Integration
### Event‑Driven Architecture using Azure Service Bus

## Overview
This repository provides a complete reference architecture for integrating a proprietary SaaS inventory system with any third‑party Warehouse Management System (WMS). The design uses:

- A canonical REST API  
- A connector SDK for WMS implementers  
- Azure Service Bus for asynchronous, reliable messaging  
- A sample WMS connector demonstrating best practices  

This architecture is:

- Scalable  
- Language‑agnostic  
- Auditable  
- Fault‑tolerant  
- Extensible  

---

## Architecture Overview

### High‑Level Flow
1. SaaS calls the Canonical API (e.g., create shipment).  
2. Canonical API publishes an event to Azure Service Bus.  
3. A WMS connector subscribes to the event.  
4. The connector translates the canonical payload → WMS API.  
5. The connector publishes a response event (e.g., ShipmentAcknowledged).  
6. Canonical API consumes the response and updates SaaS state.

### Key Components
- **CanonicalApi** — ASP.NET Core Web API exposing canonical endpoints  
- **ConnectorSdk** — shared library containing DTOs, interfaces, and Service Bus helpers  
- **SampleWmsConnector** — example connector showing how a WMS vendor would integrate  

---

## Project Structure
/src /CanonicalApi /Controllers /Services /Messaging /Models /Configuration Program.cs appsettings.json
/ConnectorSdk /Abstractions /Base /Models /Messaging ConnectorSdk.csproj
/SampleWmsConnector /Services /Messaging /Models Program.cs


---

## Canonical Data Model

### InventoryDto
- Sku  
- Location  
- Quantity  

### ShipmentRequest
- ShipmentId  
- DestinationWmsId  
- Items (list of ShipmentLine)

### ShipmentLine
- Sku  
- Quantity  

### ShipmentResponse
- ShipmentId  
- Status  
- Timestamp  

### InventoryUpdateEvent
- Sku  
- Quantity  
- Location  
- UpdatedAt  

---

## Canonical API Endpoints

### Inventory
`GET /api/inventory`  
Returns canonical inventory data.

### Shipments
`POST /api/shipments`  
Publishes a `ShipmentCreated` event to Azure Service Bus.

### Health
`GET /api/health`  
Basic health check.

---

## Connector SDK

The SDK provides:

- Canonical DTOs  
- `IWmsConnector` interface  
- `ServiceBusPublisher<T>`  
- `ServiceBusConsumer<T>`  
- Retry policies  
- Error normalization  

WMS partners can implement connectors in any language, but the SDK provides a C# reference implementation.

---

## Sample WMS Connector

Demonstrates:

- Subscribing to `shipments` topic  
- Translating canonical → WMS API  
- Publishing `ShipmentAcknowledged`  
- Publishing `InventoryUpdated` events  

---

## Getting Started

### 1. Configure Azure Service Bus
Add connection strings to `appsettings.json`:

```json
"ServiceBus": {
  "ConnectionString": "YOUR_CONNECTION_STRING",
  "ShipmentsTopic": "shipments",
  "InventoryUpdatesTopic": "inventory-updates",
  "AcknowledgementsTopic": "acknowledgements"
}

