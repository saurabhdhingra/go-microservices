# Go Microservices Ecommerce Backend

This project is a backend for an ecommerce application, built using the microservices architecture in Go. It leverages gRPC for inter-service communication and exposes a unified API via a GraphQL gateway. The system is designed for scalability, resilience, and clear separation of concerns.

## Folder Structure

```
go-microservices/
├── account/         # Account microservice (user management)
│   ├── client.go
│   ├── repository.go
│   ├── server.go
│   ├── service.go
│   ├── account.proto
│   ├── cmd/account/main.go
│   └── ...
├── catalog/         # Catalog microservice (product management)
│   ├── client.go
│   ├── repository.go
│   ├── server.go
│   ├── service.go
│   ├── catalog.proto
│   ├── cmd/catalog/main.go
│   └── ...
├── order/           # Order microservice (order processing)
│   ├── client.go
│   ├── repository.go
│   ├── server.go
│   ├── service.go
│   ├── order.proto
│   ├── cmd/order/main.go
│   └── ...
├── graphql/         # GraphQL gateway (API aggregation)
│   ├── main.go
│   ├── graph.go
│   ├── schema.graphql
│   ├── ...
├── docker-compose.yaml
├── go.mod
└── ...
```

## Common Architecture: Layered Microservices

Each microservice (`account`, `catalog`, `order`) follows a clean, layered architecture:

1. **Repository Layer**

   - Handles all data persistence and retrieval (e.g., Postgres for account/order, Elasticsearch for catalog).
   - Exposes an interface for CRUD operations.

2. **Service Layer**

   - Implements business logic, orchestrates repository calls, and enforces domain rules.
   - Exposes a service interface for use by the server layer.

3. **Server Layer (gRPC Server)**

   - Exposes the service as a gRPC API.
   - Handles incoming gRPC requests, translates them to service calls, and returns responses.

4. **Client Layer (gRPC Client)**

   - Provides a typed client for other services or the GraphQL gateway to interact with the microservice over gRPC.

5. **Proto Definitions**
   - Each service defines its own `.proto` file for gRPC contracts.

### Example Flow

- A request to create an order:
  1. Hits the GraphQL gateway.
  2. Gateway calls the order service via gRPC client.
  3. Order service validates the account and products by calling account and catalog services via their gRPC clients.
  4. Order service persists the order using its repository.

## GraphQL Gateway

- The `graphql/` directory contains the API gateway, which exposes a single GraphQL endpoint for clients.
- The gateway aggregates data from all microservices by using their gRPC clients.
- The GraphQL schema (`schema.graphql`) defines the API surface, including queries and mutations for accounts, products, and orders.
- This approach provides a unified API for frontend clients, hiding the complexity of the underlying microservices.

## Scalability & Resilience

- **Service Independence:** Each microservice can be developed, deployed, and scaled independently.
- **gRPC Communication:** Fast, strongly-typed, and efficient inter-service communication.
- **Resilient Patterns:**
  - Retry logic for connecting to databases and other services.
  - Graceful error handling and resource cleanup.
- **Stateless Services:** All services are stateless and can be horizontally scaled.
- **Data Isolation:** Each service manages its own data store, reducing coupling and blast radius.
- **API Gateway:** The GraphQL gateway can be scaled independently to handle API traffic spikes.

## Orchestration with Docker Compose

- The `docker-compose.yaml` file defines all services and their dependencies (databases, etc.).
- Services:
  - `account`, `catalog`, `order`: The three core microservices.
  - `graphql`: The GraphQL API gateway.
  - `account_db`, `catalog_db`, `order_db`: Databases for each service.
- This setup allows for easy local development and testing of the entire system.

## Getting Started

1. **Clone the repository:**
   ```sh
   git clone https://github.com/saurabhdhingra/go-microservices.git
   cd go-microservices
   ```
2. **Start all services:**
   ```sh
   docker-compose up --build
   ```
3. **Access the GraphQL Playground:**
   - Visit `http://localhost:8080/playground` in your browser.

## Extending the System

- Add new microservices by following the same layered pattern.
- Update the GraphQL gateway to expose new APIs.
- Scale services independently based on load.

## Tech Stack

- **Go** (1.24+)
- **gRPC** for service-to-service communication
- **GraphQL** (via gqlgen)
- **PostgreSQL** (account, order)
- **Elasticsearch** (catalog)
- **Docker Compose** for orchestration

---

**This architecture enables rapid development, clear separation of concerns, and robust scaling for modern ecommerce backends.**
